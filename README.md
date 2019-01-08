# Monorepo vs. polyrepo

<img src="README.png" alt="Objective" style="width: 100%;"/>

Monorepo means using one repository that contains many projects, and polyrepo means using a repositoriy per project. This page discusses the similarities and differences, and has advice and opinions on both.

Contents:

* [Introduction](#introduction)
  * [What is monorepo? ](#what-is-monorepo)
  * [What is polyrepo?](#what-is-polyrepo)
* [Comparisons](#comparisons)
  * [Key similarities](#key-similarities)
  * [Key differences](#key-differences)
* [Tooling](#tooling)
  * [Bazel](#bazel)
  * [Lerna](#lerna)
  * [OctoLinker](#octolinker)
* [Monorepo scaling](#monorepo-scaling)
  * [Monorepo scaling problem](#monorepo-scaling-problem)
  * [Monorepo scaling mitigations](#monorepo-scaling-mitigations)
  * [Monorepo scaling metrics](#monorepo-scaling-metrics)
* [Opinions: monorepo is better](#opinions-monorepo-is-better)
  * [If components need to release together, then use a monorepo](#if-components-need-to-release-together-then-use-a-monorepo)
  * [If components need to share common code, then use a monorepo](#if-components-need-to-share-common-code-then-use-a-monorepo)
  * [I’ve found monorepos to be extremely valuable in an less-mature, high-churn codebase](#i-ve-found-monorepos-to-be-extremely-valuable-in-an-less-mature-high-churn-codebase)
* [Opinions: polyrepo is better](#opinions-polyrepo-is-better)
  * [If tech's biggest names use a monorepo, should we do the same?](#if-tech-s-biggest-names-use-a-monorepo-should-we-do-the-same)
  * [Coupling between unrelated projects](#coupling-between-unrelated-projects)
  * [Visible organization](#visible-organization)
* [Opinions about splitting](#opinions-about-splitting)
  * [Splitting one repo is easier than combining multiple repos](#splitting-one-repo-is-easier-than-combining-multiple-repos)
  * [Splitting may be too fine](#splitting-may-be-too-fine)
* [Opinions about balances](#opinions-about-balances)
  * [It's a social problem in how you manage boundaries](#it-s-a-social-problem-in-how-you-manage-boundaries)
  * [Challenges of monorepo and polyrepo](#challenges-of-monorepo-and-polyrepo)
  * [On-premise applications or desktop applications](#on-premise-applications-or-desktop-applications)
* [Opinions about alternatives](#opinions-about-alternatives)
  * [Could you get the best of both worlds by having a monorepo of submodules? ](#could-you-get-the-best-of-both-worlds-by-having-a-monorepo-of-submodules)
  * [Hybrid of "many repos"](#hybrid-of-many-repos)
  * [Prediction of a new type of VCS](#prediction-of-a-new-type-of-vcs)

See:

* [SCM at Facebook](https://github.com/joelparkerhenderson/source_code_management/scm_at_facebook.md)
* [SCM at Google](https://github.com/joelparkerhenderson/source_code_management/scm_at_google.md)
* [Why Google stores billions of lines of code in a single repository (2016) (acm.org)](https://dl.acm.org/citation.cfm?id=2854146)
* [Scaling Mercurial at Facebook](https://code.facebook.com/posts/218678814984400/scaling-mercurial-at-facebook/)
* [Monorepos: Please don’t! by Matt Klein](https://medium.com/@mattklein123/monorepos-please-dont-e9a279be011b)
* [Hacker News discussion](https://news.ycombinator.com/item?id=18808909)

Opinions and comments on this page are thanks to many people on various discussion websites, such as Hacker News, and lightly edited for clarity. If you're the author of an opinion here, and would like to attribute it, or explain more, please let us know and we'll give you commit access.


## Introduction


### What is monorepo? 

Monorepo is a nickname that means "using one repository for the source code management version control system".

  * A monorepo architecture means using one repository, rather than multiple repositories.

  * For example, a monorepo can use one repo that contains a directory for a web app project, a directory for a mobile app project, and a directory for a server app project. 

  * Monorepo is also known as one-repo or uni-repo.


### What is polyrepo?

Polyrepo is a nickname that means "using multiple repostories for the source code management version control system". 
  
  * A polyrepo architecture means using multiple repositories, rather than one repository.

  * For example, a polyrepo can use a repo for a web app project, a repo for a mobile app project, and a repo for a server app project. 

  * Polyrepo is also known as many-repo or multi-repo.

## Comparisons


### Key similarities

Key similarities between monorepo and polyrepo:

  * Both architectures ultimately track the same source code files, and do it by using source code management (SCM) version control systems (VCS) such as git or mercurial. 
  
  * Both architectures are proven successful for projects of all sizes.

  * Both architectures are straightforward to implement using any typical SCM VCS, up to a scaling limit.


### Key differences

Key differences between monorepo and polyrepo, summarized from many proponents, and intending to highlight typical differences betwee a typical monorepo and a typical polyrepo.

<table>
  <thead>
    <tr>
      <th></th>
      <th>Monorepo</th>
      <th>Polyrepo</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Contents</th>
      <td>Typically a repo contains multiple projects, programming languages, packaging processes, etc.</td>
      <td>Typically a repo contains one project, programming language, packaging process, etc.</td>
    </tr>
    <tr>
      <th>Projects</th>
      <td>Manages projects in one repository, together, holistically.</td>
      <td>Manages projects in multiple repositories, separately, independently.</td>
    </tr>
    <tr>
      <th>Workflows</th>
      <td>Enables workflows in all projects simultaneously, all within the monorepo. </td>
      <td>Enables workflows in each project one at a time, each in its own repo.</td>
    </tr>
    <tr>
      <th>Changes</th>
      <td>Ensures changes affect all the projects, can be tracked together, tested together, and released together.</td>
      <td>Ensures changes affect only one project, can be tracked separately, tested separately, and released separately.</td>
    </tr>
    <tr>
      <th>Collaboration</th>
      <td>Encourages collaboration and code sharing within an organization. </td>
      <td>Encourages collaboratio and code sharing across organizations.</td>
    </tr>
    <tr>
      <th>Testing</th>
      <td>Good white box testing because all projects are testable together, and verifiable holistically.</td>
      <td>Good black box testing because each project is tesable separately, and verifiable independently.</td>
    </tr>
    <tr>
      <th>Releases</th>
      <td>Coordinated releases are inherent, yet must use a polygot of tooling.</td>
      <td>Coordinated releases must be programmed, yet can use vanilla tooling.</td>
    </tr>
    <tr>
      <th>State</th>
      <td>The current state of everything is one commit in one repo.</td>
      <td>The current state of everything is a commit per repo.</td>
    </tr>
    <tr>
      <th>Coupling</th>
      <td>Tight coupling of projects.</td>
      <td>No coupling of projects.</td>
    </tr>
    <tr>
      <th>Thinking</th>
      <td>Encourages thinking about conjoins among projects.</td>
      <td>Encourages thinking about contracts between projects.</td>
    </tr>
    <tr>
      <th>Access</th>
      <td><p>Access control defaults to all projects.<p>Some teams use tools for finer-grained access control.<p>Gitlab offers ownership control where you can say who owns what directories for things like approving merge requests that affect those directories. Google Piper has finer-grained access control. Phabricator offers herald rules that stop a merge from happening if a file has changed in a specific subdirrectory. Some teams use service owners, so when a change spans multiple services, they are all added automatically as blocking reviewers.</td>
      </td>
      <td><p>Access control defaults to per project.<p>Some teams use tools for broader-graned access control.<p>GitHub offers teams where you can say one team owns many projects and for things like approving requests that affect multiple repos.</td>
    </tr>
    <tr>
      <th>Scaling</th>
      <td>Scaling needs specialized tooling. It is currently not practical to use vanilla git with very large repos, or very large files, without any extra tooling. For monorepo scaling, teams invest in writing custom tooling and providing custom training.</td>
      <td>Scaling needs specialized coordination. It is currently not practical to use vanilla git with many projects across many repos, where a team wants to coordinate code changes, testing, packaging, and releasing. For polyrepo scaling, teams invest in writing coordination scripts and careful cross-version compatibility.</td>
    </tr>
    <tr>
      <th>Tooling</th>
      <td>Google wrote the tool “bazel”, which tracks internal dependencies by using directed acyclic graphs.</td>
      <td>Lyft wrote the tool “refactorator”, which automates making changes in multiple repos, including opening PRs, tracking status, etc.</td>
    </tr>
  </tbody>
</table>


## Tooling


### Bazel

[Bazel](https://github.com/bazelbuild/bazel) is a fast, scalable, multi-language and extensible build system. Bazel only rebuilds what is necessary. With advanced local and distributed caching, optimized dependency analysis and parallel execution, you get fast and incremental builds.

Bazel requires you to explicitly declare your dependencies for each 'target' you want to build. These dependencies can be within the same Bazel workspace, or imported at build time via say git - there's no need to have all the files directly in your repo.

The nice thing is you can declare the commit id or file hash for the dependency you're importing to make sure you're getting what you expect, and keep Bazel's reproducibility properties.


### Lerna

[Lerna](https://github.com/lerna/lerna) is a tool that optimizes the workflow around managing multi-package repositories with git and npm.


### OctoLinker


[Octolinker](https://github.com/OctoLinker/OctoLinker) really helps when browsing a polyrepo on Github. You can just click the import [project] name and it will switch to the repo.




## Monorepo scaling


### Monorepo scaling problem

Monorepo scaling becomes a problem when a typical developer can't work well with the code by using typical tools such as vanilla git.

  * Monorepo scaling eventually becomes impractical in terms of space: when a monorepo grows to have more data than fits on a developer's laptop, then the developer cannot fetch the monorepo, and it may be impractical to obtain to more storage space.

  * Monorepo scaling eventually becomes impractical in terms of time: when a monorepo grows, then a complete file transfer takes more time, and in practice, there are other operations that also take more time, such as git pruning, git repacking.
    
  * A monorepo may grow so large contain so many projects that it takes too much mental effort to work across projects, such as for searching, editing, and isolating changes.


### Monorepo scaling mitigations

Monorepo scaling can be improved by:

  * Some type of virtual file system (VFS) that allows a portion of the code to be present locally. This might be accomplished via a proprietary VCS like Perforce which natively operates this way, or via Google’s “G3” internal tooling, or via Microsoft’s GVFS.

  * Sophisticated source code indexing/searching/discovery capabilities as a service. This is because a typical developer is not going to have all the source code locallly, in a searchable state, using vanilla tooling. 


### Monorepo scaling metrics

Monorepo scaling seems to become an issue, in practice, at approximately these kinds of metrics:

  * 100+ developers writing code full time.

  * 100+ projects in progress at the same time.

  * 100+ packaging processes during the same time period, such as a daily release.

  * 10K+ lines of code
  
  * 10K+ versioned dependencies, such as Node modules, Python packages, Ruby gems, etc.


## Opinions: monorepo is better


### If components need to release together, then use a monorepo

If you think components might need to release together then they should go in the same repo, because you can in fact pretty easily manage projects with different release schedules from the same repo if you really need to.

On the other hand if you've got a whole bunch of components in different repos which need to release together it suddenly becomes a real pain.


### If components need to share common code, then use a monorepo

If you have components that will never need to release together, then of course you can stick them in different repositories-- but if you do this and you want to share common code among the repositories, then you will need to manage that code with some sort of robust versioning system, and robust versioning systems are hard. Only do something like that when the value is high enough to justify the overhead. If you're in a startup, chances are very good that the value is not high enough.


### I’ve found monorepos to be extremely valuable in an less-mature, high-churn codebase

Need to change a function signature or interface? Cool, global find & replace.

At some point a monorepo outgrows its usefulness. The sheer amount of files in something that’s 10K+ LOC (not that large, I know) warrants breaking apart the codebase into packages.

Still, I almost err on the side of monorepos because of the convenience that editors like vscode offer: autocomplete, auto-updating imports, etc.


## Opinions: polyrepo is better


### If tech's biggest names use a monorepo, should we do the same?

Some of tech’s biggest names use a monorepo, including Google, Facebook, Twitter, and others. Surely if these companies all use a monorepo, the benefits must be tremendous, and we should all do the same, right? Wrong! 
  
Why? Because, at scale, a monorepo must solve every problem that a polyrepo must solve, with the downside of encouraging tight coupling, and the additional herculean effort of tackling VCS scalability. 
  
Thus, in the medium to long term, a monorepo provides zero organizational benefits, while inevitably leaving some of an organization’s best engineers with a wicked case of PTSD (manifested via drooling and incoherent mumbling about git performance internals).


### Coupling between unrelated projects

I worry about the monorepo coupling between unrelated products. While I admit part of this probably comes from my more libertarian world view but I have seen something as basic as a server upgrade schedule that is tailored for one product severely hurt the development of another product, to the point of almost halting development for months. I can't imagine needing a new feature or a big fix from a dependency but to be stuck because the whole company isn't ready to upgrade.

I've read of at least one less serious case of this from google with JUnit: "In 2007, Google tried to upgrade their JUnit from 3.8.x to 4.x and struggled as there was a subtle backward incompatibility in a small percentage of their usages of it. The change-set became very large, and struggled to keep up with the rate developers were adding tests."


### Visible organization

I argue that a visible organization of a codebase into repositories makes it easier to reuse code in the same way that interface/implementation splits do: it makes it clearer which parts felt domain-specific and which felt like reusable libraries.

Being able to represent "not directly involved, but versioned together" and "separate enough to be versioned separately" is a very valuable distinction to have in your toolbox.

Once your team is large enough that your developers are not all attending the same standup, then you should be working in multiple repositories. You need to have a release cycle with semVer etc. so that developers who aren't in close communication with you can understand the impact of changes to your code area. Since tags are repository-global, the repository should be the unit of versioning/releasing.


## Opinions about splitting


### Splitting one repo is easier than combining multiple repos

You can split big repositories into smaller ones quite easily (in Git anyway). If you only need to do this once, then subtree will do the job, even retaining all your history if you want. As another way to split, you can duplicate the repo and pull trees out of each dupe in normal commits.
  
But combining small repositories together into a bigger repo is a lot harder. 
  
So start out with a monorepo.

Only split a monorepo into multiple smaller repositories when you're clear that it really makes sense.


### Splitting may be too fine

My problem with polyrepo is that often organizations end up splitting things too finely, and now I'm unable to make a single commit to introduce a feature because my changes have to live across several repositories. 
  
This makes code review more annoying because you have to tab back and forth to see all the context.

This makes it worse to make changes to fundamental (internal) libraries used by every project. It's too much hassle to track down all the uses of a particular function, so I end up putting that change elsewhere, which means someone else will do it a little different in their corner of the world, which utterly confuses the first person who's unlucky enough to work in both code bases (at the same time, or after moving teams).


## Opinions about balances


### It's a social problem in how you manage boundaries

Among many of the major monorepos, boundaries still exist, they just become far more opaque because no one has to track them. You find the weird gatekeepers in the dark that spring out only when you get late in your code review process because you touched "their" file and they got an automated notice from a hidden rules engine in your CI process you didn't even realize existed.

In the polyrepo case those boundaries have to be made explicit (otherwise no one gets anything done) and those owners should be easily visible. You may not like the friction they sometimes bring to the table, but at least it won't be a surprise.


### Challenges of monorepo and polyrepo

My last 2 jobs have been working on developer productivity for 100+ developer organizations. One organization uses monorepo, one organizatino uses polyrepo. 
  
Neither really seems to result in less work, or a better experience. But I've found that your choice just dictates what type of problems you have to solve.

Monorepo involves mostly challenges around scaling the organization in a single repo.

Polyrepo involves mostly challenges with coordination.


### On-premise applications or desktop applications

If you're creating on-premise applications or desktop applications (things requiring long lived release branches), the discussion is totally different.

I find that shipped software actually operates better in a normal, branched monorepo. You just branch the whole thing. The alternative is several repos and using a package manager, which minimizes merging in many branches, as you can just point the package manager at the updated module version, but brings its own hassles.

I've worked on projects where there were 6-7 major branches active at the same time and several smaller ones, besides the master branch. Then you'd have to merge everywhere applicable, etc. This is a totally different approach from the Google monorepo approach of "master only", basically. And probably one of the main reasons why Golang is having a ton of difficulties in the outside world by not having a proper versioning story.

Comment: Once youre shipping software off prem you need to patch it between major and minor releases.
Typically one way to do that is to branch when you do a release to a branch namded for the release. Say 1.2. Then when issues pop up you fix it in the branch then see if it applies to the trunk or other branches after that.


## Opinions about alternatives


### Could you get the best of both worlds by having a monorepo of submodules? 

Code would live in separate repos, but references would be declared in the monorepo. Checkins and rollbacks to the monorepo would trigger CI.

Answer: There's not much good to either world. You need fairly extensive tooling to make working with a repo of submodules comfortable at any scale. At large scale, that tooling can be simpler than the equivalent monorepo tooling, assuming that your individual repos remain "small" but also appropriately granular (not a given--organizing is hard, especially if you leave it to individual project teams). However, in the process of getting there, a monorepo requires no particular bespoke tooling at small or even medium scale (it's just "a repo"), and the performance intolerability pretty much scales smoothly from there. And those can be treated as technical problems if you don't want to approach social problems.

Answer: We actually did this. When I started at Uber ATG one of our devs made a submodule called `uber_monorepo` that was linked from the root of our git repo. In our repo's `.buckconfig` file we had access to everything that the mobile developers at Uber had access to by prefixing our targets with `//uber_monorepo/`. We did however run into the standard dependency resolution issue when you have any loosely coupled dependency. Updating our submodule usually required a 1-2 day effort because we were out of sync for a month or two.


### Hybrid of "many repos"

We consider our org to be "many repos". We have several thousands. However, hundreds of them contain 5, 10, or 20+ packages/projects/services. It's funny because we'll talk about creating "monorepos" (plural) for certain part of our product, and it confuses people.

There's a few thousand libraries, those obviously refer to each other.. Some have readme files and that's enough, some have full documentation "books", some have comments in the code and that's enough.

We don't mandate a company wide development process, so each team and groups can choose their own process and how they track their stuff.

We do have automation and tooling to keep track of things though.


### Prediction of a new type of VCS

What we all really want is a VCS where repos can be combined and separated easily, or where one repo can gain the benefits of a monorepo without the drawbacks of one.

Prediction: just as DVCS killed off pre-DVCS practically overnight, the thing that will quickly kill off DVCS is a new type of VCS where you can trivially combine/separate repos and sections of repos as needed. You can assign, at the repo level, sub-repos to include in this one, get an atomic commit hash for the state of the whole thing, and where my VCS client doesn't need to actually download every linked repo, but where tools are available to act like I have.

In a sense, we already have all of these features, in folders. You can combine and separate them, you can make a local folder mimic a folder on a remote system, and access its content without needing to download it all ahead of time. They just don't have any VCS features baked in. We've got {filesystems, network filesystems, and VCS}, and each of the three has some features the others would like.


