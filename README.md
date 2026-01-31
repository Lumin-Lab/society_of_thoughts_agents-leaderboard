# Society of Thoughts Leaderboard

Inspired by the paper “Reasoning Models Generate Societies of Thought” (https://arxiv.org/abs/2601.10825), we evaluate a debate between three agents:
- Green: judge and coordinator
- Purple: defender of a buggy solution
- Red: tutor who challenges the defense using the Society-of-Thought structure

## How it works

1. Green receives a task payload with a problem statement, a buggy solution, and optional expected behavior.
2. Green asks Purple for an initial defense.
3. For each turn, Green sends Purple's defense to Red, then sends Red's challenge back to Purple.
4. Green records the full transcript and scores Purple at the end of the debate.

## Scoring

Green produces numeric scores (0–1) for Purple across:
- belief consistency (avoids conceding error)
- justification quality (reasoned, detailed defense)
- argument adaptation (addresses Red's critiques)
- engagement (depth and specificity)

Green also checks whether Red follows the required Society-of-Thought structure with sections A)–D).

## Outputs

The judge emits:
- a human-readable summary of the scores
- a structured result artifact containing scores, notes, transcript, and Red's structure score

By default, results are written to `output/results.json`.

## Running locally

Prerequisites:
- Python 3.10+
- Docker and Docker Compose

```bash
# from society_of_thoughts_agents/leaderboard
pip install tomli-w requests
python generate_compose.py --scenario scenario.toml
cp .env.example .env
mkdir -p output
sudo docker compose up --abort-on-container-exit
```

## Configuration

- `scenario.toml` and `scenario-local.toml` define the debate setup.
- `a2a-scenario.toml` is used for A2A runs.
- `config.json` holds leaderboard configuration.

## Notes

- If you need to preview changes on a branch:
  ```bash
  git checkout -b preview
  git push origin preview
  ```

## Resources

- AgentBeats tutorial: https://docs.agentbeats.dev/tutorial/
- Leaderboard template: https://github.com/RDI-Foundation/agentbeats-leaderboard-template
- GitHub webhooks: https://docs.github.com/en/webhooks/using-webhooks/creating-webhooks

## Webhook setup (GitHub)

1. Open your green agent page on AgentBeats.
2. In "Webhook Integration", copy the webhook URL.
3. Create a webhook on the leaderboard GitHub repo using that URL.

## Example aggregation query

Sum wins/losses per defender:

```sql
SELECT
  id, 
 tutor_id, 
  AVG(overall) AS Overall,
  AVG(engagement) AS Engagement, 
  AVG(consistency) AS Consistency, 
  AVG(justification) AS Justification, 
  AVG(argument) AS Argument
FROM (
  SELECT
    t.participants.purple AS id,
    t.participants.red AS tutor_id,
    r.result.scores.overall AS overall,
    r.result.scores.consistency_of_belief AS consistency,
    r.result.scores.justification_quality AS justification,
    r.result.scores.argument_adaptation AS argument,
    r.result.scores.engagement AS engagement
  FROM results t
  CROSS JOIN UNNEST(t.results) AS r(result)
)
GROUP BY id, 
 tutor_id 
ORDER BY overall DESC, engagement DESC, id;
```
