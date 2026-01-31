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