# Society of Thoughts Leaderboard

This leaderboard evaluates the Society-of-Thoughts agents by having the green judge coordinate a multi-turn debate between the purple defender and red tutor.

## How scoring works

The green judge scores purple on:
- belief consistency
- justification quality
- argument adaptation
- engagement

It also tracks whether red follows the required Society-of-Thought structure.

## Running locally

```bash
# from society_of_thoughts_agents/leaderboard
pip install tomli-w requests
python generate_compose.py --scenario scenario.toml
cp .env.example .env
mkdir -p output
sudo docker compose up --abort-on-container-exit
```

The results will be written to `output/results.json`.

git checkout -b preview
git push origin preview

https://docs.agentbeats.dev/tutorial/

https://github.com/RDI-Foundation/agentbeats-leaderboard-template


Setting Up Webhooks on Leaderboard github repo
This next set of steps allows your leaderboard to automatically update when new results are pushed to the repo.

First, navigate to your green agent page on AgentBeats. Open the box titled “Webhook Integration” and copy the webhook URL.

https://docs.github.com/en/webhooks/using-webhooks/creating-webhooks


SELECT
      id,
      SUM(win) AS Wins,
      SUM(loss) AS Losses
    FROM (
      SELECT
        t.participants.defender AS id,
        r.result.passed_tests_count as win,
        r.result.failed_tests_count as loss
      FROM results t
      CROSS JOIN UNNEST(t.results) AS r(result))
    GROUP BY id
    ORDER BY wins DESC, losses ASC, id;