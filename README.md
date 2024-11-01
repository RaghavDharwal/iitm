Here is the code

import pandas as pd

# Load data
users_df = pd.read_csv("users.csv")
repos_df = pd.read_csv("repositories.csv")

# Answers

# 1. Top 5 users in Stockholm with the highest number of followers
top_5_followers = users_df.nlargest(5, 'followers')['login'].tolist()
answer_1 = ", ".join(top_5_followers)

# 2. 5 earliest registered GitHub users in Stockholm
earliest_users = users_df.nsmallest(5, 'created_at')['login'].tolist()
answer_2 = ", ".join(earliest_users)

# 3. 3 most popular licenses
top_licenses = repos_df['license_name'].dropna().value_counts().nlargest(3).index.tolist()
answer_3 = ", ".join(top_licenses)

# 4. Company with the most users
top_company = users_df['company'].mode().iloc[0]
answer_4 = top_company

# 5. Most popular programming language
top_language = repos_df['language'].mode().iloc[0]
answer_5 = top_language

# 6. Second most popular programming language among users who joined after 2020
recent_users = users_df[users_df['created_at'] > '2020-01-01']
second_language = repos_df[repos_df['login'].isin(recent_users['login'])]['language'].value_counts().nlargest(2).index[-1]
answer_6 = second_language

# 7. Language with the highest average number of stars per repository
language_avg_stars = repos_df.groupby('language')['stargazers_count'].mean().idxmax()
answer_7 = language_avg_stars

# 8. Top 5 users by leader strength (followers / (1 + following))
users_df['leader_strength'] = users_df['followers'] / (1 + users_df['following'])
top_5_leader_strength = users_df.nlargest(5, 'leader_strength')['login'].tolist()
answer_8 = ", ".join(top_5_leader_strength)

# 9. Correlation between number of followers and public repositories
correlation_followers_repos = users_df['followers'].corr(users_df['public_repos'])
answer_9 = round(correlation_followers_repos, 3)

# 10. Regression slope of followers on public repositories
from scipy.stats import linregress
slope, _, _, _, _ = linregress(users_df['public_repos'], users_df['followers'])
answer_10 = round(slope, 3)

# 11. Correlation between having projects enabled and wikis enabled
projects_wiki_corr = repos_df['has_projects'].corr(repos_df['has_wiki'])
answer_11 = round(projects_wiki_corr, 3)

# 12. Average difference in following for hireable vs. non-hireable users
hireable_following_diff = users_df[users_df['hireable'] == True]['following'].mean() - users_df[users_df['hireable'] == False]['following'].mean()
answer_12 = round(hireable_following_diff, 3)

# 13. Regression slope of followers on bio word count (excluding missing bios)
users_df['bio_word_count'] = users_df['bio'].fillna("").apply(lambda x: len(x.split()))
slope_bio_followers, _, _, _, _ = linregress(users_df[users_df['bio_word_count'] > 0]['bio_word_count'], users_df[users_df['bio_word_count'] > 0]['followers'])
answer_13 = round(slope_bio_followers, 3)

# 14. Top 5 users by repositories created on weekends (UTC)
repos_df['created_at'] = pd.to_datetime(repos_df['created_at'])
repos_df['is_weekend'] = repos_df['created_at'].dt.dayofweek >= 5
weekend_repos = repos_df[repos_df['is_weekend']]
top_5_weekend_repos = weekend_repos['login'].value_counts().nlargest(5).index.tolist()
answer_14 = ", ".join(top_5_weekend_repos)

# 15. Fraction difference of users with email when hireable vs. non-hireable
hireable_email_fraction = users_df[users_df['hireable'] == True]['email'].notna().mean()
non_hireable_email_fraction = users_df[users_df['hireable'] == False]['email'].notna().mean()
answer_15 = round(hireable_email_fraction - non_hireable_email_fraction, 3)

# 16. Most common surname
users_df['surname'] = users_df['name'].dropna().apply(lambda x: x.split()[-1])
common_surnames = users_df['surname'].value_counts()
most_common_surname = ", ".join(common_surnames[common_surnames == common_surnames.max()].index.tolist())
answer_16 = most_common_surname

# Print answers
answers = {
    "1": answer_1, "2": answer_2, "3": answer_3, "4": answer_4, "5": answer_5,
    "6": answer_6, "7": answer_7, "8": answer_8, "9": answer_9, "10": answer_10,
    "11": answer_11, "12": answer_12, "13": answer_13, "14": answer_14, "15": answer_15,
    "16": answer_16
}

for q, ans in answers.items():
    print(f"Answer {q}: {ans}")

xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx


1.- Data collected using GitHub API
- Date of collection: 2024-10-23
- Only included users with 100+ followers
- Up to 500 most recently pushed repositories per user


