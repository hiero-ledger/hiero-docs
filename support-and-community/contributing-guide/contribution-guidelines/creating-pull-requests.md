---
description: >-
  A pull request is a way to suggest changes in our repository. A PR allows the
  team to discuss your changes, review code, and provide feedback before merging
  it into the main branch.
---

# Creating Pull Requests

If you'd like to propose changes directly to the documentation, you can submit a pull request. Here's how:

1. **Fork the Repository:** Navigate to the `hedera-docs` [repository](https://github.com/hashgraph/hedera-docs) and click the "Fork" button at the top right. This creates a copy of the repository in your GitHub account.
2. **Clone the Forked Repository:** Clone the forked repository to your local system and make changes. Be sure to follow the repository's coding and style guidelines.
3. **Commit Your Changes:** Once you've made your changes, commit them with a clear, detailed message describing the changes you've made.&#x20;
   1. Use [sign-off](https://github.com/hashgraph/.github/blob/main/CONTRIBUTING.md#sign-off) when making each of your commits.&#x20;
      1. Alternatively, you can use auto sign-off by installing `cp hooks-git/prepare-commit-msg .git/hooks && chmod +x .git/hooks/prepare-commit-msg`
   2. Use [this guide](https://pre-commit.com/#3-install-the-git-hook-scripts) to install the pre-commit hook scripts to check for files with names that would conflict on a case-insensitive filesystem like MacOS HFS+ or Windows FAT.
4. **Push Your Changes:** Push your committed changes to your forked repository on GitHub.
5. **Submit a Pull Request:** Back in the `hedera-docs` repository, click the "Pull Requests" tab and then the "New pull request" button. Select your forked repository and the branch containing your changes, then click "Create pull request".
6. **Describe Your Changes:** Give your pull request a title and describe the proposed changes. This description should make it clear why the changes should be incorporated.
7. **Submit the Pull Request:** Click the "Create pull request" button to submit it. We'll review your proposed changes and, if they're approved, merge them into the repository.

By logging issues and creating pull requests, you're helping us make the Hedera documentation better for everyone. We appreciate your contributions and look forward to collaborating with you!

{% hint style="info" %}
**Note:** The Hedera team will review issues and pull requests.
{% endhint %}
