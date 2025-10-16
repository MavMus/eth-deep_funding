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
As LLM's output was not explicitly a numeric response, I had to use a function for extracting its rating, which was from 1 to 100. Now, we normalised such that the final weight when summed across each repo was 1.

Things which I would have loved to explore,
# Explore different probabilistic models for comparison between a pair

