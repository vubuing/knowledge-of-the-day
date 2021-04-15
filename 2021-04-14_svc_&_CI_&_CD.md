Today, I learnt about Source Version Control & CI/CD.

# **I. Source Version Control (SVC)**

- It's like a time machine. We can save and reload anytime we want to. We can work in parallel universes of our source code, experimenting without fear of losing work, rolling back if something goes wrong.

- Use git. Git is one of the most popular distributed source version control. Github for open source works, playbook & practices. Gitlab for storing source code.

#### **Branching**

- There is only one eternal branch master/develop/current/next/... - whatever.

- All other branches (feature, release, hotfix, and whatever else you need) are temporary & only uses as a convenience to share code with other devs and as a backup measure. They are always removed once the changes present on them land on master.

### **1. Write A Feature**

- Create a local `feature/` branch based off `develop`.

```
git checkout develop
git pull
git checkout -b feature/<branch-name>
```

- **Rebase frequently** to incorporate upstream changes.

```
git fetch origin
git rebase develop
```

- **Resolve conflicts** when feature is complete & tests pass, stage the changes & commit them.

```
git add --all
git status
git commit --verbose
```

### **2. Merge/Rebase**

If you're created more than 1 commit, use git rebase [interactively](https://docs.github.com/en/github/getting-started-with-github/about-git-rebase) to squash like "Fix whitespace" into cohesive commits with good messages:

```
git fetch origin
git rebase -i origin/master
git push --force-with-lease origin <branch-name>
```

### **3. Write A Good Commit Message**

Your commit messages are your personal legacy. They are the record of why and what you've done to a codebase. A commit message should **follow this format**

```
type (scope): subject
```

In which,

- type: action
- scope (optional): the part of project you're working on
- subject: short description of the commit

[Read more](https://chris.beams.io/posts/git-commit/)

Once your work is committed, submit a **WIP pull request**.

### **4. Pull Request (PR)**

#### **1. Open a WIP PR as early as possible**

A perfect impl of the wrong specification is worthless. Before doing anything, write down how you want to implement the feature and submit for review.

-> It gives you chance to **think through the solution** without the overhead of having to change code every time you change your mind about how something should be organized

#### **2. Open a PR as early as possible**

PR are great ways to start a conversation of a feature, so start one as soon as possible - even before you are finished the code. Your team can comment on the feature as it evolves, instead of providing all feedback at the very end.

### **5. Review PR & Feature**

Practice using PR in Github or merge request in Gitlab to send in code for new features. The PR would be merged if passes conditions of:

- [Code Review](https://github.com/vubuing/knowledge-of-the-day/blob/main/2021-04-13.md) is done on the request before merging into the master
- [Feature Review](https://github.com/vubuing/knowledge-of-the-day/blob/main/2021-04-14.md#1-write-a-feature)

### **6. Release**

The master branch should be in production ready at all times. Use git tag with [semantic version](https://github.com/dwarvesf/playbook/blob/master/engineering/versioning.md) when it's about time to release.

### **7. Use Template**

To make things easier, we have adopted Issue template and Pull Request template that we think they are great to help the team to improve the productivity

- [Issue Template](https://github.com/dwarvesf/.github/blob/master/ISSUE_TEMPLATE.md)
- [Pull Request Template](https://github.com/dwarvesf/.github/blob/master/PULL_REQUEST_TEMPLATE.md)

### **Q & A**

**Q: I just committed a secret - how can I remove it from the git history?**

A: Use git reset to remove them from the commit history. If you already pushed your changes to a public branch, ensure that all contributors that pulled these changes will pull the now updated branch.

If you have published these changes to a public repo like GitHub, you will first need to change the secrets. Then follow the instructions to [remove sensitive data from a repository](https://docs.github.com/en/github/authenticating-to-github/removing-sensitive-data-from-a-repository). This can be accomplished through the git filter-branch command or using the BFG Repo-Cleaner and both options are covered in the instructions.

**Q: What if the client wants their own repository?**

A: If a client has their own GitHub team and wishes to own the repository, there will be a fork of the project that should be used for all issues, pull requests, comments, etc.

# **II. Continuous Integration**

### **1. What**

- Continuous Integration (CI) is a dev practice where dev integrate code into a shared repo frequently
- Each integration then be verified by automated build & automated tests
- One of the key benefits of integrating regularly is that you can **detect errors quickly** & locate them more easily. As each **change** introduced is typically **small**, **pinpointing the specific change** that introduced a **defect** can be done quickly

### **2. Importance**

- Without CI, devs must manually coordinate & communicate from when they are contributing code to the end product.
- Without robust CI pipeline, **communication** between product & engineering can be **cumbersome**. It will make it **harder** for engineering to **estimate time of delivery on requests** because the **time to integrate** new changes becomes an unknown risk

### **3. CI at X**

- Write tests, mostly integration: we don't do TDD, but we do write
- PR & Code Review
  - Our **CI system** handles both individual **commit pushs** & **PRs**
  - In case of PRs: create **separated deployment** preview (for web apps) -> everyone can visit & verify if everything works as intended
- Optimize pipeline speed
  - Customized Docker image: have a **dedicated Docker image** which includes **pre-building steps**, e.g. migration tools & CLI binaries like `goose`, `psql`, `yarn`, `prettier`
  - Dependencies caching: cached go vendor or `node_modules` dir can significantly **improve pipeline speed**
- Collect build **artifacts**: helpful to re-verify previous build manually

# **III. Continuous Delivery**

- Continuous integration, deployment & delivery
  - 3 phases of automated software release pipeline
  - These take software from idea to delivery to the end-user
  - Integration phase: 1st step
- Dev teams have seen enormous benefits from implementing CI & continuous delivery (CD) as well as continuous deployment practices. By setting up a CI/CD pipeline, teams can merge small changes often, release code to prod frequently & feel confident doing so cause any changes trigger a build that is tested automatically before being released

### **1. CD at X**

- The process for releasing/deploying software MUST be **repeatable & reliable**
- **Automate everything** - automate all the tasks you repeatedly do, & this tends to lead to reliability
- **Done means "released"**
- Build quality comes first - take the time to **invest** in your **quality** metrics. A project with good, targeted quality metrics will invariably be better than one without, & easier to maintain in the long run
- **Tagging** your **builds** & **smoke test** your **deployments**

# **IV. Setup CI/CD Pipeline**

- Pick your own CI platform: Gitlab CI, Circle CI or Bitrise for iOS
- Write the CI instruction file: Most of the time the conf file should be a YAML file; file structure depends on CI platform
- Stages to specify: build, test, packaging & push artifacts
- CI Server will trigger the target server to run the deployment script. Depends on the current setup, we may use Ansible or K8s to make it happen
- For mobile apps, the deployment may be varied. Some will use Fastlane to build & Fabric/Testflight for app distribution

# **V. Checklist**

### **1. CI**

- The pipeline covers test, build, packaging & push artifacts stage
- **CI environment** is **cleaned** from starting
- **Pipeline** is as **fast** as possible
- Use **cache** (vendor, node_modules, etc.)
- **Save artifacts after building** stage
- **Environment variables won't display** when running CI/CD
- When building the app with **Docker**, use **alpine image**
- When building the app with **Docker**, the image should have **tag**:
  - as `develop` for `develop` branch
  - as `semantic versioning` when releasing tag & mark it as latest as well. For FE, you may need to build more than 1 version for a released tag, e.g. v1.0.0-rc, v1.0.0-beta
- For **mobile**, remember to write a **script** to have **increase build number** automatically
- The **API target** should be configured **appropriately** to reflect the **current build environment**. Avoiding pointing to different API version or environment

### **2. CD**

- Make sure **env variables up-to-date** after deploying
- Use **image tag** in different envs:
  - develop use tag `develop`
  - staging use tag `rc` or v1.0.0-rc (for FE)
  - production use `specific semantic tag` e.g. v1.0.0
- Make sure the **deployment** has been shipped to **correct env** (prod, staging, etc.)
- For **dev env**, the **db** has been setup **auto migrate after deploying new version**
