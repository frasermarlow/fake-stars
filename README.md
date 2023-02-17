# dagster-fakestar README.md

This is a simple Dagster project to analyze the number of fake GitHub stars on any GitHub repository.

### Install instructions [rough edit]

#make the virtual env
python3 -m venv venv
source venv/bin/activate

#Install Dagster and our other dependencies
Python3 -m pip install dagster dagit
pip install PyGithub
pip install pandas

Import this repo.
Execute this one-file project with the command:

```commandline
GITHUB_ACCESS_TOKEN=<<GITHUB_ACCESS_TOKEN>> DAGSTER_HOME=~/<<PROJECT_FOLDER>>>>/ dagster dev -f __init__.py
```

### Specifying the repo to analyze

You can specify the repository you want to analyze on line 8 by replacing the values in 
```commandline
ORCHESTRATOR = {'name':'mage',  'repo': 'mage-ai/mage-ai'}
```

`name` is arbitrary and `repo` is part tf the public url for your target respository. 

### Explanation of the repo

This repo is a Dagster project and involves 4 assets:

1) Asset `stargazers`: We call the GitHub API and retrieve a list of users who have starred the repo at the top of the file.
2) Asset `this_repo_stargazers_list`: We turn the GItHub response into a Python list of usernames
3) Asset `this_repo_gh_stars`: We look up each user in turn and pull their detailed profile
4) Asset `stargazers_dataframe`: We analyze each profile and match to our heuristic to determine if they are fake or not

In addition to the above, we have ops:

a) `validate_star`: Matching a profile against the heuristic  
b) `see_if_user_exists`: Verifying that a user still exists before pulling the full details  
c) `handle_exception`: Handling exceptions for the GitHub api call.  This op calls on `get_retry_at` which returns the `x-ratelimit-reset`value for the GitHub API.  

Currently, the pipeline will simply return a result in the Dagster UI as in `INFO` event type such as "Score is 12.34% fake stars" and will provide a list of usernames flagged as fake.
