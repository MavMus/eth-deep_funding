# Here is the write-up for the current Quantifying Contributions of Open Source Projects to the Ethereum Universe
Username on Pond: MavMus <br />
email id: kumarankaj110@gmail.com


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


```
