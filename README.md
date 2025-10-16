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

```python
# Compute high multiplier threshold (mean + 3*std)
multipliers_arr = np.array([m if m is not None else 0 for m in multipliers])
high_mult_threshold = multipliers_arr.mean() + 3 * multipliers_arr.std()


# Separate high-multiplier and regular reasonings
high_mult_reasonings = [
    r for r, m in zip(reasonings, multipliers)
    if isinstance(r, str) and r.strip() and m is not None and m > high_mult_threshold
]
regular_reasonings = [
    r for r, m in zip(reasonings, multipliers)
    if isinstance(r, str) and r.strip() and (m is None or m <= high_mult_threshold)
]

```
