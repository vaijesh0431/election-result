# election-result
import pandas as pd

# Sample election results data
data = {
    "Party": ["Party A", "Party B", "Party A", "Party B", "Party C", "Party C", "Party A", "Party B", "Party A"],
    "Candidate": ["Candidate X", "Candidate Y", "Candidate Z", "Candidate P", "Candidate Q", "Candidate R", "Candidate S", "Candidate T", "Candidate U"],
    "Votes": [15000, 12000, 18000, 13000, 10000, 11000, 17000, 14000, 16000],
    "Constituency": ["A", "A", "B", "B", "C", "C", "D", "D", "E"]
}

# Creating a DataFrame
df = pd.DataFrame(data)

# Calculate total votes for each party
total_votes_by_party = df.groupby("Party")["Votes"].sum()
print("Total Votes per Party:\n", total_votes_by_party)

# Identify the winning party in each constituency
def get_winning_party(x):
    return x.loc[x['Votes'].idxmax(), "Party"]

winning_party_by_constituency = df.groupby("Constituency").apply(get_winning_party)
print("\nWinning Party by Constituency:\n", winning_party_by_constituency)

# Determine the overall election winner
overall_winner = total_votes_by_party.idxmax()
print("\nOverall Election Winner:", overall_winner)

# Calculate vote share percentages
total_votes = df["Votes"].sum()
df["Vote Share (%)"] = (df["Votes"] / total_votes) * 100
print("\nVote Share Percentages:\n", df[["Candidate", "Votes", "Vote Share (%)"]])

# Identify constituencies with close contests (margin less than 12%)
def close_contest(x):
    vote_counts = x["Votes"].values
    if len(vote_counts) > 1:
        vote_counts.sort()
        margin = (vote_counts[-1] - vote_counts[-2]) / vote_counts[-1] * 100
        return margin < 12.0
    return False

close_constituencies = df.groupby("Constituency").filter(close_contest)["Constituency"].unique()
print("\nConstituencies with Close Contests:", close_constituencies)
