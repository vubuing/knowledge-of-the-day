# I. Code Review

**Read more**: [Google Standard of Code Review](https://google.github.io/eng-practices/review/reviewer/standard.html)

> Ensure correctness, quality of the code and learning opportunities for assignee and reviewer.

Every project team member must have another team member/team leader to review code. Every changes to the code must be in `PR` to the main dev branch. On larger project, require 2 or more reviewers.

We review everything on the `PR`: code, tests, documents, config files, etc.

### **1. Hierarchy of Needs**

Most important first:

- Mental Alignment
- Correct Solution
- Design Discussion
- Find Bugs
- Style

### **2. Code Review Flow**

- Feature/Bug assignee finished coding on a new branch based off `develop` branch
- Assignee self-review the code one last time before assigning to reviewer
- Assignee open a `PR` on project repo and assign the `PR` to a reviewer, removes any [WIP] prefix in PR title to let reviewer knows this PR is ready for review
- Assignee write down brief information about this `PR`
- Reviewer read & review PR's information, make sure to understand why do we have this `PR`
- Reviewer review/refactor the code with assignee
- **Reviewer signs off** on the `PR` with comment, e.g. "Ready to merge"
- **Developer** merges `PR` and clean up remote branch

### **3. Principles**

- Technical facts & data overrule opinions and personal preferences
- **Style**: must follow the style guide. Any purely style point (whitespace, etc.) that is not in the style guide is a matter of personal preference. The style should be consistent. If there is no previous style, authors' accepted
- Beside, if the author can demonstrate that several approached are equally valid, then the reviewer should accept the preference of the author. Otherwise the choice is dictated by standard principles of software design
- If no other rule applies, then the reviewer may ask the author to be consistent with what is in the current codebase, as long as that doesn't worsen the overall code health of the system

### **4. Code Review Standard**

At minimal:

- The code must work and tests must pass
- No typos
- No debugging code i.e. console.log/fmt.Print/etc. unless you have a good reason for it
- Never commit env files to source control, unless you have a good reason for it

#### **4.1. About testing**

- We don't follow TDD (Test Driven Development), we don't need to have tests for everything but we must have tests to cover **critical parts**.
- **Test cases** ensure that our app is **working** and **without bugs** whenever we introduce new features into it.
- **Test cases** also gives us the affordability to **refactor** & improve **code quality**. With refactoring, we learn better coding practices & improve ourselves as a coder.

#### **4.2. Naming convention**

- **Depends** on each programming language

### **5. Reviewer Guides**

- Review code, don't review coder
- Check for correctness of code, not how you would do it
- Discuss possibilities & trade-offs, not what's right & what's wrong
- Code review should take less than 30 mins

#### **5.1. Mentoring**

- Code review can have important function of **teaching** dev something new about a language, a framework, or general software design principles
- Good to leave comments that help a dev learn something new
- Sharing knowledge
- Should leave comment to express that something could be better, but not critical to meet the standards in this docs, prefix with **"Nit: "**

#### **5.2. Comments & Feedbacks**

- **Courtesy**: Always comments about the **code** & never making comments about the **developer**
- **Explain WHY**: Comment should explain your reason helps the dev understand why you are making your comment.
- **Giving Guidance**:
  - Appropriate balance between pointing out problems & providing direct guidance
  - However, sometimes direct instructions, suggestions, or even code are more helpful. The primary goal of code review is to get the best code/solution possible. The 2nd goal is improving skills of dev so that they require less review over time
- **Accepting Explanation**:
  - If ask dev **explain** a piece of code that you don't understand, should usually result in them rewriting the code more **clearly**. Explanations written only in code review tool are not helpful to future code readers
  - **Encourage** dev to **simplify code** & **add code comments** instead of just explaining the complexity to you
