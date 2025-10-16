# Here is the write-up for the current Quantifying Contributions of Open Source Projects to the Ethereum Universe
Username on Pond: MavMus <br />
Email ID: kumarankaj110@gmail.com

Trying to pick hints from the problem statement,
1. Usage of External Dataset
2. Juror Specific 


Trying different Approaches:
* Traditional Machine Learning Algorithms
* A blend of LLM’s reasoning and ML
* Probabilistic comparison of pairs


As others have already pointed the dataset was quite partial due to which atleast earlier I was limited to only the blend of feature engineering and  combining of ML based algorithms, I changed my approach when the reasoning of each jurors were introduced in the training dataset (15th September update), Shifting my approach to more of letting a LLM trying to imitate a juror, based on the reasonings of all other jurors. <br />

Basic Feature engineering
```python
contributor_names = enhanced_teams.assign(Names = enhanced_teams['recentContributors'].str.split(', ')).explode('Names')
contributor_names

repo_with_contributor = pd.merge(
    contributor_names,
    enhanced_contributors,
    left_on = 'Names',
    right_on = 'name',
    how = 'left'
)

repo_with_contributor

avg_followers_of_contributor_per_repo = r_c.groupby('githubLink_x')['followers'].mean().reset_index(name='total_avg_followers_by_contributor')

avg_commit_of_contributor_per_repo = r_c.groupby('githubLink_x')['commitCount_y'].mean().reset_index(name='total_avg_commit_by_contributor')

avg_activity_of_contributor_per_repo = r_c.groupby('githubLink_x')['activity_y'].mean().reset_index(name = 'total_avg_activity_by_contributor')

avg_total_stars_of_contributor_per_repo = r_c.groupby('githubLink_x')['totalStars_y'].mean().reset_index(name = 'total_avg_stars_by_contributor')

avg_reputation_of_contributor_per_repo = r_c.groupby('githubLink_x')['reputation_y'].mean().reset_index(name = 'total_avg_reputation_by_contributor')
```
Taking inspiration from earlier competition based on a similar theme, where participants have successfully leveraged the publicly available dataset from GitHub
```python
import requests
HEADERS = {
    'Accept': 'application/vnd.github.v3+json',
    'Authorization': f'token {GITHUB_TOKEN}'
}
if 'test' not in globals() or 'repo' not in test.columns:
    raise ValueError("The 'test' DataFrame with a 'repo' column must be defined before running this cell.")

REPOS = test['repo'].tolist()
results = []

for repo_slug in REPOS:
    print(f"Fetching data for {repo_slug}...")

    if repo_slug.startswith("https://github.com/"):
        owner_repo = repo_slug.replace("https://github.com/", "")
    else:
        owner_repo = repo_slug

    
    repo_url = f"https://api.github.com/repos/{owner_repo}"
    repo_response = requests.get(repo_url, headers=HEADERS)
    if repo_response.status_code == 200:
        repo_data = repo_response.json()
    else:
        print(f"Failed to fetch repo data for {repo_slug}: {repo_response.status_code}")
        print("Response:", repo_response.json())
        repo_data = {}

    
    search_query = f'"github.com/{owner_repo}" in:file'
    search_url = f"https://api.github.com/search/code?q={search_query}"
    search_response = requests.get(search_url, headers=HEADERS)
    if search_response.status_code == 200:
        search_data = search_response.json()
    else:
        print(f"Failed to fetch search data for {repo_slug}: {search_response.status_code}")
        print("Response:", search_response.json())
        search_data = {}

    commits_url = f"https://api.github.com/repos/{owner_repo}/commits?since=2024-10-06T00:00:00Z&per_page=1"
    commits_response = requests.get(commits_url, headers=HEADERS)
    if commits_response.status_code != 200:
        print(f"Failed to fetch commits for {repo_slug}: {commits_response.status_code}")
        print("Response:", commits_response.json())

    repo_info = {
        "repo": repo_slug,
        "description": repo_data.get('description') if repo_data else None,
        "stars": repo_data.get('stargazers_count') if repo_data else None,
        "last_updated": repo_data.get('updated_at') if repo_data else None,
        "adoption_count": search_data.get('total_count', 0) if search_data else 0
    }

    results.append(repo_info)

```


Creating a reasoning_summary based on the juror’s reasoning, which will be used to gauge what’s going on behind the minds of jurors. Apart from consolidating the reasoning of the jurors, I also decided to put an extra weight on those reasons that had some large multipliers, 
```python
# Compute high multiplier threshold (mean + 3*std)
multipliers_arr = np.array([m if m is not None else 0 for m in multipliers])
high_mult_threshold = multipliers_arr.mean() + 3 * multipliers_arr.std()


# Separate high-multiplier and regular reasoning
high_mult_reasonings = [
    r for r, m in zip(reasonings, multipliers)
    if isinstance(r, str) and r.strip() and m is not None and m > high_mult_threshold
]
regular_reasonings = [
    r for r, m in zip(reasonings, multipliers)
    if isinstance(r, str) and r.strip() and (m is None or m <= high_mult_threshold)
]

```
Apart from the summary, I also decided to extract some of the key points that jurors were repeating too often as a basis for their judgement, later on these points were explicitly mentioned in my prompt.

Depending on whether a particular repository is an execution client, consensus client, light client, development framework, SDK, or package, I decided to support the model by fetching the data accordingly. 
Now the judgements were done within those groups only, like within execution_client, consensus_client repos, I looked for which repo has a higher market share. For packages, which repos have the most downloads?


```python
# --- DATA (Manually collected data, as writing a scraping script for this limited number was pointless) ---
client_market_data = {
    'geth': 61.72, 'nethermind': 18.57, 'erigon': 7.17, 'besu': 6.44,
    'reth': 5.56, 'lighthouse': 44.89, 'prysm': 24.68, 'teku': 11.98,
    'nimbus': 10.01, 'lodestar': 2.18, 'grandine': 0.28
}


package_downloads = {
    "ethers-io/ethers.js": 1750502, "nomicfoundation/hardhat": 74467,
    "openzeppelin/openzeppelin-contracts": 463048, "wevm/viem": 1787316,
    "ethereum/remix-project": 687, "apeworx/ape": 14234,
    "ethereum/web3.py": 714558, "vyperlang/titanoboa": 495,
    "alloy-rs/alloy": 76000
}
```
The prompts and code used are present in the repo as well, not including it here as it would make it unnecessarily long post. <br />
As the LLM's output was not explicitly a numeric response, I had to use a function to extract its rating, which ranged from 1 to 100. Now, we normalised such that the final weight when summed across each repo was 1.

Things which I would have loved to explore,
Explore different probabilistic models for comparison between a pair, supplementing the model with more feature engineering. Trying some advanced reasoning-based LLM... 

I would try to add more in this repo, feedbacks would be appreciated.
Regards

