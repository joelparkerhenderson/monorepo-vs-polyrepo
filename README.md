# Monorepo vs. polyrepo

Contents:

* [Introduction](#introduction)
  * [What is monorepo? ](#what-is-monorepo)
  * [What is polyrepo?](#what-is-polyrepo)
* [Comparisons](#comparisons)
  * [Key similarities](#key-similarities)
  * [Key differences](#key-differences)
* [Monorepo scaling](#monorepo-scaling)
  * [Monorepo scalling problem](#monorepo-scalling-problem)
* [Monorepo scaling mitigations](#monorepo-scaling-mitigations)
* [Monorepo scaling metrics](#monorepo-scaling-metrics)
* [Opinions](#opinions)
  * [If components need to release together, then use a monorepo](#if-components-need-to-release-together-then-use-a-monorepo)
  * [If components need to share common code, then use a monorepo](#if-components-need-to-share-common-code-then-use-a-monorepo)
  * [I’ve found monorepos to be extremely valuable in an less-mature, high-churn codebase](#i-ve-found-monorepos-to-be-extremely-valuable-in-an-less-mature-high-churn-codebase)
  * [If tech's biggest names use a monorepo, should we do the same?](#if-tech-s-biggest-names-use-a-monorepo-should-we-do-the-same)
  * [Blindly copying](#blindly-copying)
  * [Coupling between unrelated projects](#coupling-between-unrelated-projects)
  * [We don't use a monorepo, and it's hell](#we-don-t-use-a-monorepo-and-it-s-hell)
  * [Splitting one repo is easier than combining multiple repos](#splitting-one-repo-is-easier-than-combining-multiple-repos)
  * [Splitting may be too fine.](#splitting-may-be-too-fine)
  * [OctoLinker really helps when browsing a polyrepo on Github](#octolinker-really-helps-when-browsing-a-polyrepo-on-github)
  * [It's a social problem in how you manage boundaries](#it-s-a-social-problem-in-how-you-manage-boundaries)
  * [Challenges of monorepo and polyrepo](#challenges-of-monorepo-and-polyrepo)
  * [Could you get the best of both worlds by having a monorepo of submodules? ](#could-you-get-the-best-of-both-worlds-by-having-a-monorepo-of-submodules)

See:

* [Why Google stores billions of lines of code in a single repository (2016) (acm.org)](https://dl.acm.org/citation.cfm?id=2854146)
* [Scaling Mercurial at Facebook](https://code.facebook.com/posts/218678814984400/scaling-mercurial-at-facebook/)
* [Monorepos: Please don’t! by Matt Klein](https://medium.com/@mattklein123/monorepos-please-dont-e9a279be011b)
* [Hacker News discussion](https://news.ycombinator.com/item?id=18808909)


## Introduction


### What is monorepo? 

Monorepo is a nickname that means "using one repository for the source code management version control system".

  * A monorepo architecture means using one repository, rather than multiple repositories.

  * For example, a monorepo can use one repo that contains a directory for a web app project, a directory for a mobile app project, and a directory for a server app project. 

  * Monorepo is a.k.a. one-repo or uni-repo.


### What is polyrepo?

Polyrepo is a nickname that means "using multiple repostories for the source code management version control system". 

  * A polyrepo architecture means using multiple repositories, rather than one repository.

  * For example, a polyrepo can use a repo for a web app project, a repo for a mobile app project, and a repo for a server app project. 

  * Polyrepo is a.k.a. many-repo or multi-repo.  


## Comparisons


### Key similarities

Key similarities between monorepo and polyrepo:

  * Both architectures ultimately track the same source code files, and do it by using source code management (SCM) version control systems (VCS) such as git or mercurial. 
  
  * Both architectures are proven successful for projects of all sizes.

  * Both architectures are straightforward to implement using any typical SCM VCS, up to a scaling limit.


### Key differences

Key differences between monorepo and polyrepo, summarized from many proponents:

<table>
  <thead>
    <tr>
      <th>Monorepo</th>
      <th>Polyrepo</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>Manages projects in one repository, together, holistically.</td>
      <td>Manages projects in multiple repositories, separately, independently.</td>
    </tr>
    <tr>
      <td>A repo contains multiple projects, programming languages, packaging processes, etc.</td>
      <td>A repo contains one project, programming language, packaging process, etc.</td>
    </tr>
    <tr>      
      <td>Ability to work in all projects simultaneously, all within the monorepo. </td>
      <td>Ability to work in each project one at a time, each in its own repo.</td>
    </tr>
    <tr>      
      <td>Ensures changes affect all the projects, can be tracked together, tested together, and released together.</td>
      <td>Ensures changes affect only one project, can be tracked separately, tested separately, and released separately.</td>
    </tr>
    <tr>      
      <td>Easier collaboration and code sharing within an organization. </td>
      <td>Easier collaboration and code sharing across organizations.</td>
    </tr>
    <tr>      
      <td>Good white box testing because all projects are testable together, and verifiable holistically.</td>
      <td>Good black box testing because each project is tesable separately, and verifiable independently.</td>
    </tr>
    <tr>
      <td>Coordinated releases are inherent, yet must use a polygot of tooling.</td>
      <td>Coordinated releases must be programmed, yet can use vanilla tooling.</td>
    </tr>
    <tr>
      <td>One commit in one repo is the current state of all projects.</td>
      <td>Each commit in each repo is the current state of each project.</td>
    </tr>
    <tr>
      <td>Tight coupling of projects.</td>
      <td>No coupling of projects.</td>
    </tr>
    <tr>
      <td>Encourages thinking about conjoins among projects.</td>
      <td>Encourages thinking about contracts between projects.</td>
    </tr>
    <tr>
      <td>Access control defaults to all projects. Some teams use tools for finer-grained access control. Gitlab offers ownership control where you can say who owns what directories for things like approving merge requests that affect those directories. Google Piper has finer-grained access control. Phabricator offers herald rules that stop a merge from happening if a file has changed in a specific subdirrectory. Some teams use service owners, so when a change spans multiple services, they are all added automatically as blocking reviewers.</td>
      </td>
      <td>Access control defaults to per project. Some teams use tools for broader-graned access control. GitHub offers teams where you can say one team owns many projects and for things like approving requests that affect multiple repos.</td>
    </tr>
    <tr>
      <td>Monorepo scaling necessitates specialized tooling. For example, it is currently not practical to use vanilla git with very large repos, or very large files, without any extra tooling. For monorepo scaling, teams invest in writing custom tooling and providing custom training. An example is Google writing the “bazel” tool, which tracks internal dependencies by using directed acyclic graphs.</td>
      <td>Polyrepo scaling necessitates specialized coordination. For example, it is currently not practical to use vanilla git with many projects across many repos, where a team wants to coordinate code changes, testing, packaging, and releasing. For polyrepo scaling, teams invest in writing coordination scripts and careful cross-version compatibility. An example is Lyft writing the “refactorator” tool, which automates making changes in multiple repos, including opening PRs, tracking status, etc.</td>
    </tr>
    <tr>
  </tbody>
</table>


## Monorepo scaling


### Monorepo scalling problem

Monorepo scaling becomes a problem when a typical developer can't work well with the code by using typical tools such as vanilla git.

  * Monorepo scaling eventually becomes impractical in terms of space: when a monorepo grows to have more data than fits on a developer's laptop, then the developer cannot fetch the monorepo, and it may be impractical to obtain to more storage space.

  * Monorepo scaling eventually becomes impractical in terms of time: when a monorepo grows, then a complete file transfer takes more time, and in practice, there are other operations that also take more time, such as git pruning, git repacking.
    
  * A monorepo may grow so large contain so many projects that it takes too much mental effort to work across projects, such as for searching, editing, and isolating changes.


## Monorepo scaling mitigations

Monorepo scaling can be improved by:

  * Some type of virtual file system (VFS) that allows a portion of the code to be present locally. This might be accomplished via a proprietary VCS like Perforce which natively operates this way, or via Google’s “G3” internal tooling, or via Microsoft’s GVFS.

  * Sophisticated source code indexing/searching/discovery capabilities as a service. This is because a typical developer is not going to have all the source code locallly, in a searchable state, using vanilla tooling. 


## Monorepo scaling metrics

Monorepo scaling seems to become an issue, in practice, at approximately these kinds of metrics:

  * 100+ developers writing code full time.

  * 100+ projects in progress at the same time.

  * 100+ packaging processes during the same time period, such as a daily release.

  * 10K+ lines of code
  
  * 10K+ versioned dependencies, such as Node modules, Python packages, Ruby gems, etc.


## Opinions


### If components need to release together, then use a monorepo

I'd probably go further and say that if you just think components might need to release together then they should go in the same repo, because you can in fact pretty easily manage projects with different release schedules from the same repo if you really need to.

On the other hand if you've got a whole bunch of components in different repos which need to release together it suddenly becomes a real pain.


### If components need to share common code, then use a monorepo

If you have components that will never need to release together, then of course you can stick them in different repositories-- but if you do this and you want to share common code among the repositories, then you will need to manage that code with some sort of robust versioning system, and robust versioning systems are hard. Only do something like that when the value is high enough to justify the overhead. If you're in a startup, chances are very good that the value is not high enough.


### I’ve found monorepos to be extremely valuable in an less-mature, high-churn codebase

Need to change a function signature or interface? Cool, global find & replace.

At some point a monorepo outgrows its usefulness. The sheer amount of files in something that’s 10K+ LOC (not that large, I know) warrants breaking apart the codebase into packages.

Still, I almost err on the side of monorepos because of the convenience that editors like vscode offer: autocomplete, auto-updating imports, etc.


### If tech's biggest names use a monorepo, should we do the same?

Some of tech’s biggest names use a monorepo, including Google, Facebook, Twitter, and others. Surely if these companies all use a monorepo, the benefits must be tremendous, and we should all do the same, right? Wrong! 
  
Why? Because, at scale, a monorepo must solve every problem that a polyrepo must solve, with the downside of encouraging tight coupling, and the additional herculean effort of tackling VCS scalability. 
  
Thus, in the medium to long term, a monorepo provides zero organizational benefits, while inevitably leaving some of an organization’s best engineers with a wicked case of PTSD (manifested via drooling and incoherent mumbling about git performance internals).


### Blindly copying

I see companies that want to "google scale" and blindly copy the idea of monorepos but without the requisite tooling teams or cloud computing background / infrastructure that makes this possible.


### Coupling between unrelated projects

I worry about the monorepo coupling between unrelated products. While I admit part of this probably comes from my more libertarian world view but I have seen something as basic as a server upgrade schedule that is tailored for one product severely hurt the development of another product, to the point of almost halting development for months. I can't imagine needing a new feature or a big fix from a dependency but to be stuck because the whole company isn't ready to upgrade.

I've read of at least one less serious case of this from google with JUnit: "In 2007, Google tried to upgrade their JUnit from 3.8.x to 4.x and struggled as there was a subtle backward incompatibility in a small percentage of their usages of it. The change-set became very large, and struggled to keep up with the rate developers were adding tests."


### We don't use a monorepo, and it's hell

I may make a change to X, but some other developer doesn't know it, and then one day when he makes a change to X and tries to pull those changes into Y, now he has to change the bits of Y which are affected by my changes as well as his.

I would love to have a monorepo, although I'd advocate for branch-based rather than trunk-based development. 

Yes, merges can be their own kind of hell, but they impose that cost on the one making breaking changes, rather than everyone else.


### Splitting one repo is easier than combining multiple repos

You can split big repositories into smaller ones quite easily (in Git anyway). If you only need to do this once, then subtree will do the job, even retaining all your history if you want. As another way to split, you can duplicate the repo and pull trees out of each dupe in normal commits.
  
But combining small repositories together into a bigger repo is a lot harder. 
  
So start out with a monorepo.

Only split a monorepo into multiple smaller repositories when you're clear that it really makes sense.


### Splitting may be too fine.

My problem with polyrepo is that often organizations end up splitting things too finely, and now I'm unable to make a single commit to introduce a feature because my changes have to live across several repositories. 
  
This makes code review more annoying because you have to tab back and forth to see all the context.

This makes it worse to make changes to fundamental (internal) libraries used by every project. It's too much hassle to track down all the uses of a particular function, so I end up putting that change elsewhere, which means someone else will do it a little different in their corner of the world, which utterly confuses the first person who's unlucky enough to work in both code bases (at the same time, or after moving teams).


### OctoLinker really helps when browsing a polyrepo on Github


You can just click the import [project] name and it will switch to the repo.

See https://github.com/OctoLinker/OctoLinker


### It's a social problem in how you manage boundaries

Among many of the major monorepos, boundaries still exist, they just become far more opaque because no one has to track them. You find the weird gatekeepers in the dark that spring out only when you get late in your code review process because you touched "their" file and they got an automated notice from a hidden rules engine in your CI process you didn't even realize existed.

In the polyrepo case those boundaries have to be made explicit (otherwise no one gets anything done) and those owners should be easily visible. You may not like the friction they sometimes bring to the table, but at least it won't be a surprise.


### Challenges of monorepo and polyrepo

My last 2 jobs have been working on developer productivity for 100+ developer organizations. One organization uses monorepo, one organizatino uses polyrepo. 
  
Neither really seems to result in less work, or a better experience. But I've found that your choice just dictates what type of problems you have to solve.

Monorepo involves mostly challenges around scaling the organization in a single repo.

Polyrepo involves mostly challenges with coordination.


### Could you get the best of both worlds by having a monorepo of submodules? 

Code would live in separate repos, but references would be declared in the monorepo. Checkins and rollbacks to the monorepo would trigger CI.

Answer: There's not much good to either world. You need fairly extensive tooling to make working with a repo of submodules comfortable at any scale. At large scale, that tooling can be simpler than the equivalent monorepo tooling, assuming that your individual repos remain "small" but also appropriately granular (not a given--organizing is hard, especially if you leave it to individual project teams). However, in the process of getting there, a monorepo requires no particular bespoke tooling at small or even medium scale (it's just "a repo"), and the performance intolerability pretty much scales smoothly from there. And those can be treated as technical problems if you don't want to approach social problems.

Answer: We actually did this. When I started at Uber ATG one of our devs made a submodule called `uber_monorepo` that was linked from the root of our git repo. In our repo's `.buckconfig` file we had access to everything that the mobile developers at Uber had access to by prefixing our targets with `//uber_monorepo/`. We did however run into the standard dependency resolution issue when you have any loosely coupled dependency. Updating our submodule usually required a 1-2 day effort because we were out of sync for a month or two.
