---
layout: post
title: Less cognitive load when working with git
gh-repo: patrykwozinski/patrykwozinski.github.io
gh-badge: [follow]
tags: [git, version control]
comments: true
share-img: "/img/blog/dx-and-working-with-git.jpg"
---

Hi! After a long time, I'd like to share with you one of my thoughts. Pull Requests, Code Reviews, version control, and git - that's buzzwords with whom we are so familiar on daily software engineering work. That's of course not hard to commit some changes, push to the git repository and then open a pull request, true. After all, we're merging these changes to some branches like master or development, etc. - of course, it depends on your project's git-flow. Nothing interesting, that's the thing that we're teaching interns and juniors, yep.

<p align="center">
    <img src="/img/blog/dx-and-working-with-git.gif" alt="Git Pull Request"/>
</p>

But... I think it's not that easy as we think, there is one interesting thing that I've considered in the last few days. What's your approach to creating commits and merging? Have you ever thought about it? What's the logic behind your commits and decision about how to merge -> using squash or not? That's the topic that I'd like to cover today! :)

At first, let's talk about the commits - how to split them and how to cover in my opinion the best way to present changes in your code. If you're starting with a task -> let's say you want to cover the task like "New feature request: adding an opinion about the doctor". It's huge to do on this topic, that's sure. Let's stick for a moment in this place. It's worth dividing this issue into a few smaller and treat the topic as epic. After some time we could discover subtasks there like:
create an API of this action
prepare the mock implementation of higher-level components
prepare implementation details like all the side-effected elements (database, queues, etc)
As you see I'm a fan of the top-to-the-bottom approach in software engineering.

Let's make the new branch from the main development (master or develop - up to your company's process) for a whole epic like `add-doctor-opinion`. Every subtask of this issue should be committed separately. If it's a bigger thing to do - just create a branch from your task branch. Just treat branches as a possibility to hiding implementation details - it's like an encapsulation of the knowledge in programming. If your sub-branch is really to merge - every commit should represent something important at this level of abstraction. In our example those commits could look like:
API tests to cover business needs
Lightweight implementation of the API
Fix XYZ in ABC
Thanks to this people who are reviewing your code can do it step by step going deeper by commits in implementation details and check your thinking way. If everything is ready and your team approves the code - merge using squash. I love squashing commits because it shows changes without knowledge leaks for the future coders. Of course - if they would need to know more - they can open your archived pull request and check all the commit-steps in your thinking process. Less cognitive load => happier coders #devExperience!

Okay, so you've merged all the changes on the specific level of abstraction into your main task branch and now it's time to go to the production. That's also an amazing time to merge using squash because you still need to hide implementation details from the future code-readers. Every change you did is important but especially for you and code reviewers at this moment - if people from the future need more information about your changes they should view your Pull Request and check what was happened here on the specific level of abstraction. In this situation, they also might go deeper into the sub-pull-requests of your subtasks.

This approach to working with git could maximize the productivity of your team, future code readers, and also it's a huge help for code reviewers so thanks to this the work can be much more enjoyable. Remember -> your code is an implementation detail - your commit names can show higher-level descriptions of decisions. That's a huge gain for Developer Experience in my opinion!
