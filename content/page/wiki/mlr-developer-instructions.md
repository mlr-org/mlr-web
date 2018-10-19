---
date: 2017-09-28T08:00:00+06:00
title: mlr Developer instructions
authors: ["patrick-schratz"]
slug: mlr-developer-instructions
menu:
   main:
      parent: wiki
weight: -1
toc: true
---

# mlr coding guidelines

We use a git “gatekeeper” workflow model, where every code change to the master branch, whether from the main developers or outside contributors, should be a pull request, which is then checked and possibly refined through reviews. How this works in detail is outlined below. If you have questions, feel free to ask in the tracker, we are happy to help.

- Every change to the code **must** be a pull request. Lars, Michel and Bernd have the license to directly push to the master branch and merge PRs. But it is strongly encouraged that they issue pull requests, too. Nobody should merge his own PR.
- To update a branch, pull in the latest updates from the master into your branch with a **merge**. Do not rebase, especially if multiple people are working on the same branch.
- Use a descriptive title. Add some text explaining what you did and what the purpose is. Include examples of any new output, plots, etc. And please refer to the issue you are dealing with by a link in the text. Including the number in the title is good, but this is not clickable.
- Every major change to **mlr’s core system**, i.e., training, resampling, wrappers, etc, has to be **reviewed by 2 persons**.
  - The pull request can contain multiple commits, they will be squashed when the pull request is merged. Since this is done automatically by GitHub, it's not necessary for you to squash to 1 commit yourself.
- Here is a minimal check list before pull requests can be merged. Do **not** deviate from this without asking / a proper reason!
  - Travis passes. But also **always** check the output for NOTES and WARNINGS from R.
  - Unit tests added/changed as appropriate. **Every** detected bug, major addition or change must result in a **new, good test**. If your test relies on specific learner behavior, use a [mock learner](https://github.com/mlr-org/mlr/blob/master/tests/testthat/helper_mock_learners.R).
  - Did you think carefully about the names (especially exported functions) that you introduced? This is very important and hard to change later.
  - Please use the proper Roxygen tags/templates in the documentation. See [this directory](https://github.com/mlr-org/mlr/tree/master/man-roxygen) for templates we use.
  - Did you document all arguments and return values, including their types? Did you include references to relevant papers?
  - Please include examples on how the new function can be used in the documentation.
  - Did you use the stringi functions for string operations?
  - Did you use the appropriate functions for argument checking (some provided by mlr `check*`, others by the checkmate package).
  - mlr provides many functions to get information from its objects. Please use those instead of `$`.
  - NEWS: Is it an API / behavior change w.r.t. to the prior version? Mention what should be in NEWS in the pull request please, the person who merges the PR will put this in NEWS. Please don't modify NEWS directly as this tends to cause merge conflicts.
  - Code readable, commented and follows [style guide](https://github.com/rdatsci/PackagesInfo/wiki/R-Style-Guide)?
  - Is it an API change? Has the documentation at **all** relevant places been adapted? This includes the tutorial.
  - **The old API cannot be changed so existing client code breaks**. Sometimes such a change is unavoidable and preferable to improve the structure and the exported names of the package. Then use the “deprecate” mechanism explained below.
  - Make sure that no document files (*.rd), NAMESPACE file(s) are changed, as they will be updated automatically.
  - Make sure that only files are changed that are related to the PR. It can happen from time to time that your editor will add/remove whitespaces or indentation automatically.
  - Make sure that no spelling errors are in the documentation. Run a spellchecker (in RStudio you can use F7) 

## General rules

Read those if you are new to the project.

- Use a proper editor for programming.
  Like vim, emacs, sublime, RStudio.  
- Read and follow the [style guide](https://github.com/rdatsci/PackagesInfo/wiki/R-Style-Guide). 
  Yes, really. Bernd hates cleaning up such stuff behind others. 
  On Linux / Mac you can use the `quicklint` script in the `thirdparty` directory to check your changes before you submit them. 
  If you use RStudio, these settings will help:
    - Whitespace after, e.g., `if` and `for` can be automatically identified in RStudio: \
    [*Tools -> Global Options... -> Code -> Diagnostics*](https://support.rstudio.com/hc/en-us/articles/205753617-Code-Diagnostics?version=1.0.136&mode=desktop) \
    and activate "Show diagnostics for R" and "Provide R style diagnostics (e.g. whitespace)".
    - For proper indentation go to: \
      *Tools -> Global Options... -> Code -> Editing* \
       and deactivate "Vertically align arguments in auto-indent".
    - To automatically remove whitespace go to: \
      *Tools -> Global Options... -> Code -> Saving* \
       and activate "Ensure that source files end with newline" and "Strip trailing horizontal whitespace when saving".
    - Check also the Options in \
      *Tools -> Project Options... -> Code Editing* \
      and activate "Ensure that source files end with newline" and "Strip trailing horizontal whitespace when saving".
- Whatever you implement, you will document in roxygen. Look at other functions to see how this works. Input / Output? What happens in the method? Mention really important details? Like "@family", dislike "@seealso". In summary: Be brief, but precise and helpful to the user!
- Every longer, more complex operation get commented properly in code. See style guide.
- Every function that implements functionality described in the literature should be explained and the relevant literature cited.
  - Explain the high-level concept in the documentation so that reading the paper is not necessary for a basic understanding.
  - Cite the paper using roxygen @references [Author A], [Author B], and [Author C]; [Title: Subtitle], [Journal Volume x] ([Year]), ..., [?Pages].
- We like [Michel's "rt" tool](https://github.com/rdatsci/rt) here. Maybe you like it too?
- Before you push you will run at least once at the end
  - The relevant unit tests. Often this is the group "base" with "rtest --filter=base".
  - "R CMD check" or "rcheck". No errors, warnings, notes!
- Your unit tests and R examples will be the perfect compromise between
  - They test / demo everything relevant.
  - They run really fast. Maybe in much less then a second. Yes, sometimes difficult, but work on it.
- If you find a bug, always do this: Reproduce via test, THEN repair. Then make sure test runs. Reread the code piece again you touched. Can the structure be improved? If this can be done quickly, do it now. For more complex stuff: Open up a clearly understandable issue. Best with a minimal example that reproduces the bug.
- If you do change the API:
  - Deprecate the old code, use [`.Deprecated()`](https://stat.ethz.ch/R-manual/R-devel/library/base/html/Deprecated.html) at the beginning of the deprecated method. This only outputs a warning, it doesn’t automatically call the new function.
  - Carefully explain what you did so we can add that information to NEWS.
- Reference functions in package that are in suggests using `::`, e.g., `package::function`, but do not explicitly reference functions in packages that are imported.
- Please use a spellchecker, especially for documentation. In RStudio you can start a spellcheck with F7.              

Setup & Contribution Guidelines
=========

Read this if you are really new to software development in general.

Did you find a bug? Is your favourite learner missing and you want to add it? Or do you have another idea for making mlr better? We welcome all contributions to mlr. While modifying or extending such a large project can seem daunting at first, here are some guidelines on how to get started.

## Version control setup

We assume that git is already installed on your local machine, if not follow [these guidelines](https://help.github.com/articles/set-up-git/). 

Use `git clone` to clone this repo to your local machine:
```sh
git clone https://github.com/mlr-org/mlr.git
```
<br>
`cd` into cloned repo:
```sh
cd mlr
```

You should have R set up on your local machine. mlr uses quite a number of other packages (in particular, a large number of packages that provide learners). To extend mlr, you don't need all of them, but it may nevertheless help to install mlr from CRAN with all dependencies and "suggests" packages.

If you're using [RStudio](http://www.rstudio.com/products/rstudio/), you can import mlr as a project by clicking on file -> New Project -> Existing Directory (Browse the mlr project).

### Forking

If you want to make changes to the mlr code that you want to make public or submit back to us, you should
fork the main repository to your account, using the **Fork** button on the top right corner.

Use `git clone` to clone your forked repo to your local machine:
(replace 'your_username' with your github username)

```sh
git clone https://github.com/your_username/mlr.git
```
<br>
`cd` into cloned repo:
```sh
cd mlr
```
<br>
Set the `upstream` to mlr parent repo:

The easiest way is to use the https url:
```sh
git remote add upstream https://github.com/mlr-org/mlr.git
```

or if you have ssh set up you can use that url instead:
```sh
git remote add upstream git://github.com/mlr-org/mlr.git
```

### Branching

When you develop a new feature, it is recommended to create a new branch for it. Don't modify the *master* branch directly to make the provenance of any new code/feature clear.
```sh
git branch new_branch
```

When committing changes to the code, please provide meaningful commit messages that let other people know what you've done. If the commit is linked to an issue, provide the number of the issue in the message.

Once you're happy with your changes, you may want to pull in the latest changes from the *master* branch.
Move to the *master* branch, fetch the upstream changes and merge.
```sh
git checkout master
git fetch upstream
git merge upstream/master
```

Then change back to your new branch and merge your changes.
```sh
git checkout new_branch
git merge master
```
Now all changes from the *master* will be in your current branch.


### Merging the *master* in you branch
This happens quite often and is mostly done wrong. Here is one way to do it correctly:

Firstly, we build the NAMESPACE and Documentation Files automatically on the master branch, which means that if you pull the master branch you have the latest version of the NAMESPACE and all .Rd Files (which are most likely not up to date on your branch, as you shouldn't add/commit any of these).

If you merge the master in your branch all of these files will appear as modified ("green" in git status). You can just commit these as they are identical on the master and will not change the PR. 
BUT(!!!): This is only the case if you don't have any modified files in your branch before(!) you merge the master. Otherwise strange stuff can and will happen.

General workflow:

- git checkout master
- git pull 
- git checkout YOURBRANCH
- git add XXX
- git commit -m "stuff" # commit everything you need from your branch, everthing else should be deleted
- git reset HEAD --hard #This removes/deletes all uncommited files, make sure to add+commit everything you want to keep
- rm XXX # remove all files that are untracked as they might give you mergeconflicts
- git merge master
- (potentially resolve merge conflicts -> use a tool for that -> http://meldmerge.org/)
- git commit -m "resolved mergeconflicts | merged master"
- git push origin YOURBRANCH

If all of that was already clear for you: great :)
Also if you have a slightly different workflow (e.g. use git clean) that works the same way, keep doing that. Above is just how I tend to do it.

## Updating docs/ in a PR

If the tutorial was updated in docs/ in the `master` branch, do the following:

1. git checkout master
2. git pull master
3. git checkout <your-branch>
4. git checkout master -- docs/

This will **reset** the `docs/` directory in your branch with the state of the `master` branch. 
Be careful if you made changes to the tutorial yourself, these will be overwritten as well!
In this case you might want to use a normal `git merge` instead of a hard reset.

## Testing

mlr has a lot of tests for all sorts of functionality. Unfortunately, this makes it quite hard to run as a lot of packages need to be installed for everything to pass. There are several ways to run the tests locally:

- Use `devtools::test` with a filter, for example `devtools::test(filter = "ModelMultiplexer")` to check a particular file and later, when that runs, check whether your code affected other parts of mlr run the test group "base": `devtools::test(filter = "base")`. There are more tests, but the main functionality is covered by the "base" group.
- You can also run tests from the command line, with a fully installed development version of mlr. You can use the [rt tool](https://github.com/rdatsci/rt) for this, for example `rtest --filter=ModelMultiplexer` or `rtest --filter=base`.

If you want to run certain tests from `tests/testthat` line-by-line for debugging purposes, make sure that you have package `BBmisc` loaded. Otherwise, certain helper function will not be found. Also, optionally source the helper file `tests/testthat/helper_objects`. This file initiates data which is used in some tests, e.g. `classif_binomial`. 

To make really sure, we run Travis CI for every commit and pull request. This is your safety net that will check everything for you, so don't worry if you absolutely cannot get something to work on your machine!

## Contributing

Once you're happy with your code, please open a [pull request for the main repository](https://github.com/mlr-org/mlr/pulls). This will automatically run [Travis CI](https://travis-ci.org/mlr-org/mlr) on your changes to see if it still builds and all the tests pass. The developer team will get notified automatically of your pull request.

For some common tasks like integrating another learner or performance measure we have manuals in section "Extend" of the [mlr tutorial](https://mlr.mlr-org.com/index.html). 

# Build process hints and FAQ

1. Travis timeouts and package cache.
    1. We currently have a travis timelimit of 100 minutes (instead of 50) due to Lars asking about this here:
    https://github.com/travis-ci/travis-ci/issues/7173
    1. The mlr travis job installs really many packages, as mlr has really many under SUGGESTS. That can take a very long time on an "empty" machine.
    1. Fortunately, travis supports an R package cache, that we have enabled. So for each new build, only new, updated packages are installed from CRAN.
    1. Assuming that not many new suggested packages have to be updated, a current build takes about 30 min. 
    1. We have a slight problem if the R version on travis is updated, as the package then becomes empty. And we can hit the walltime just because of dependency installation (+ tests). So extra 50 min might help us here already, but if that does not work one can do this:
    Delete the "before_script" and "after_success" section from the travis.yml, and overwrite the 
    "script" section (which would normally check and test the package) with "script: - echo "DONE".       
    Then delete like 50% of the packages from DESCRIPTION. This will partially fill the cache for the 
    next run.

# Github workflow

## Tags
For every issue assign one tag of each category:
* **priority**: 
  * [Low](https://github.com/mlr-org/mlr/labels/prio-low): unimportant stuff like small enhancements 
  * [Medium](https://github.com/mlr-org/mlr/labels/prio-medium): everything that is not low, high or blocking
  * [High](https://github.com/mlr-org/mlr/labels/prio-high): Mainly bugs
  * [blocking](https://github.com/mlr-org/mlr/labels/prio-blocking): Thing like master is broken, basic functionality of mlr is wrong, blocks other high prio PRs.
* **effort**: 
  * [simplefix](https://github.com/mlr-org/mlr/labels/effort-simplefix): Things you can do in a coffee break.
  * [hardfix](https://github.com/mlr-org/mlr/labels/effort-hardfix): You probably need more than a day and a coworker.
  * no tag
* **Type**: 
  * [question](https://github.com/mlr-org/mlr/labels/type-question): Questions that can be answered without touching the code.
  * [enhancement](https://github.com/mlr-org/mlr/labels/type-enhancement): Requests or Suggestions of new functionality.
  * [bug](https://github.com/mlr-org/mlr/labels/type-bug): Functionality broken or not working like advertised.
  * [doc](https://github.com/mlr-org/mlr/labels/type-documentation): Request for a better documentation. Function code does not have to be touched.

Each PR should be tagged with one of the following tags:
* **PR state**: 
  * [work in progress](https://github.com/mlr-org/mlr/labels/work%20in%20progress%20-%20not%20done): The assigned person is working on it. If no person is assigned this PR is looking for an asignee.
  * [ready for merge](https://github.com/mlr-org/mlr/labels/ready%20for%20merge%20%28%3F%29): The PR has been reviewed by one person, who thinks that this is ready to be merged.
  * [please review](https://github.com/mlr-org/mlr/labels/please%20review): The OP has finished work on PR and wants a review or help from a random project member. 
* **priority**:
  * [Low](https://github.com/mlr-org/mlr/pulls?q=is%3Aopen+is%3Apr+label%3Aprio-low)
  * [Medium](https://github.com/mlr-org/mlr/pulls?q=is%3Aopen+is%3Apr+label%3Aprio-medium)
  * [High](https://github.com/mlr-org/mlr/pulls?q=is%3Aopen+is%3Apr+label%3Aprio-high)
  * [blocking](https://github.com/mlr-org/mlr/pulls?q=is%3Aopen+is%3Apr+label%3Aprio-blocking)

## Projects
Try to always put Issues and PRs into their according Projects.
In those projects the issues and PRs should be ordered according to their priority or order in which they have to be solved.

# Visualization guidelines

Visualizations are constructed by calling functions prefixed by "plot" followed by the class name of the object (minus "Data") to be plotted. Most of the objects used by plot functions are created by generation functions, which are used when creating the plot data requires more than simple extraction of elements from another object. In cases where generation is not necessary a "get" function is used, which simply extracts the plot-relevant aspects of another object.

An example is `plotThreshVsPerf`. Data for this plot is generated by `generateThreshVsPerfData` which returns an object of class `ThreshVsPerf`. All of these objects are S3 lists.

Performance measures all have long names as well as short names (the "name" and "id" elements respectively). Plot functions which plot measures have a logical argument `pretty.names` which is `TRUE` by default which uses the long name in place of the short name.

If multiple performance measures have the same name/id, their aggregation method is appended to their name: e.g., "Accuracy: Test mean."

In general it is preferable to use long names where space constraints do not make things unreadable, however, this should be controlled by a logical argument to the function.

Smart defaults are to be preferred over user control via long lists of arguments. The generation and get functions allow users to construct custom plots if needed; the goal of the plot functions is not to provide customizable publication-quality plots out of the box. Instead the plots should be informative enough to be used interactively. In particular there shouldn't be arguments to plot functions that control things that don't contribute to interactive usability and are easily controlled by the user.

# Learner

In MLR, a **learner** is an objects that contains a model type with specialized hyperparameters. A leaner can be created using makeLearner(), trained using train(), and used for prediction using predict().

A learner consists of a list with a rather complicated substructure. Below is an example of the learner "k-Nearest Neighbor":

```sh
root
 |--id = "classif.kknn"
 |--type = "classif"
 |--package = "!kknn"
 |--properties = c("twoclass","multiclass","numerics","factors","prob")
 |--par.set 
 |   |--pars
 |   |  |--k
 |   |  |   |--id = "k"
 |   |  |   |--type = "integer"
 |   |  |   |--len = 1
 |   |  |   |--lower = 1
 |   |  |   |--upper = Inf
 |   |  |   |--values = NULL
 |   |  |   |--cnames = NULL
 |   |  |   |--allow.inf = FALSE
 |   |  |   |--has.default = TRUE
 |   |  |   |--default = 7
 |   |  |   |--trafo = NULL
 |   |  |   |--requires = NULL
 |   |  |   |--tunable = TRUE
 |   |  |   |--special.vals = list()
 |   |  |   |--when = "train"
 |   |  |--distance
 |   |  |   |--id = "distance"
 |   |  |   |--type = "numeric"
 |   |  |   |--len = 1
 |   |  |   |--lower = 0
 |   |  |   |--upper = Inf
 |   |  |   |--values = NULL
 |   |  |   |--cnames = NULL
 |   |  |   |--allow.inf = FALSE
 |   |  |   |--has.default = TRUE
 |   |  |   |--default = 2
 |   |  |   |--trafo = NULL
 |   |  |   |--requires = NULL
 |   |  |   |--tunable = TRUE
 |   |  |   |--special.vals = list()
 |   |  |   |--when = "train"
 |   |  |--kernel
 |   |  |   |--id = "kernel"
 |   |  |   |--type = "discrete"
 |   |  |   |--len = 1
 |   |  |   |--lower = NULL
 |   |  |   |--upper = NULL
 |   |  |   |--values
 |   |  |   |   |--rectangular = "rectangular"
 |   |  |   |   |--triangular = "triangular"
 |   |  |   |   |--epanechnikov = "epanechnikov"
 |   |  |   |   |--biweight = "biweight"
 |   |  |   |   |--triweight = "triweight"
 |   |  |   |   |--cos = "cos"
 |   |  |   |   |--inv = "inv"
 |   |  |   |   |--gaussian = "gaussian"
 |   |  |   |   |--optimal = "optimal"
 |   |  |   |--cnames = NULL
 |   |  |   |--allow.inf = FALSE
 |   |  |   |--has.default = TRUE
 |   |  |   |--default = "optimal"
 |   |  |   |--trafo = NULL
 |   |  |   |--requires = NULL
 |   |  |   |--tunable = TRUE
 |   |  |   |--special.vals = list()
 |   |  |   |--when = "train"
 |   |  |--scale
 |   |  |   |--id = "scale"
 |   |  |   |--type = "logical"
 |   |  |   |--len = 1
 |   |  |   |--lower = NULL
 |   |  |   |--upper = NULL
 |   |  |   |--values
 |   |  |   |   |--TRUE = TRUE
 |   |  |   |   |--FALSE = FALSE
 |   |  |   |--cnames = NULL
 |   |  |   |--allow.inf = FALSE
 |   |  |   |--has.default = TRUE
 |   |  |   |--default = TRUE
 |   |  |   |--trafo = NULL
 |   |  |   |--requires = NULL
 |   |  |   |--tunable = TRUE
 |   |  |   |--special.vals = list()
 |   |  |   |--when = "train"
 |   |--forbidden = NULL
 |--par.vals = Named list()
 |--predict.type = "prob"
 |--name = "k-Nearest Neighbor"
 |--short.name = "kknn"
 |--note = ""
 |--config = list()
 |--fix.factors.prediction = FALSE
```

# List of possible enhancements to mlr

Here is a (surely not complete) list of possible enhancements to mlr which we would like to support in the nearer future. 

We especially welcome others to contribute, so feel free to add your ideas below, including any relevant information about the possible extension and why it may be important for users.  

We also welcome anyone to work on any of the enhancements mentioned on this page. Before you start working on integrating anything below, please open an issue in our [issue tracker](https://github.com/mlr-org/mlr/issues) and let us know, so we can update this page and ensure effort is not duplicated.    

If you are a new developer, please first check out our [coding guidelines](https://github.com/mlr-org/mlr/wiki/mlr-Coding-Guidelines) and [style guide](https://github.com/rdatsci/PackagesInfo/wiki/R-Style-Guide) before working on any of the enhancements below.

# New Learners From Existing R Package

- Check out the tutorial guide for [custom learners](https://mlr.mlr-org.com/articles/tutorial/create_learner.html) for guidance

|  |      Number      |   Package      |                     Method                            | Description |
:--|  :----------:    |   :---------:  |                :-----------------:                    |   :------:  |
1  |        adaptDA   |        amdai   |                         Adaptive Mixture Discriminant Analysis   |  |
2  |            arm   |     bayesglm   |                              Bayesian Generalized Linear Model   |            |
3  |          binda   |        binda   |                                   Binary Discriminant Analysis   |            |
4  |     bnclassify   |         awnb   |                Naive Bayes Classifier with Attribute Weighting   |            |
5  |     bnclassify   |        awtan   | Tree Augmented Naive Bayes Classifier with Attribute Weighting   |            |
6  |     bnclassify   |   nbDiscrete   |                                         Naive Bayes Classifier   |            |
7  |     bnclassify   |     nbSearch   |                           Semi-Naive Structure Learner Wrapper   |            |
8  |     bnclassify   |          tan   |                          Tree Augmented Naive Bayes Classifier   |            |
9  |     bnclassify   |    tanSearch   |Tree Augmented Naive Bayes Classifier Structure Learner Wrapper   |            |
10 |            bst   |        bstLs   |                                           Boosted Linear Model   |            |
11 |            C50   |         C5.0   |                                                           C5.0   |            |
12 |          caret   |          bag   |                                                   Bagged Model   |            |
13 |          caret   |     bagEarth   |                                                    Bagged MARS   |            |
14 |          caret   |      pcaNNet   |                        Neural Networks with Feature Extraction   |            |
15 |        caTools   |   LogitBoost   |                                    Boosted Logistic Regression   |            |
16 |     elasticnet   |         enet   |                                                     Elasticnet   |            |
17 |          enpls   |        enpls   |                      Ensemble Partial Least Squares Regression   |            |
18 |         evtree   |       evtree   |                            Tree Models from Genetic Algorithms   |            |
19 |        fastICA   |          icr   |                               Independent Component Regression   |            |
20 |           foba   |         foba   |                       Ridge Regression with Variable Selection   |            |
21 |            gam   |          gam   |                                    Generalized Additive Models   |            |
22 |           gpls   |         gpls   |                              Generalized Partial Least Squares   |            |
23 |            hda   |          hda   |                          Heteroscedastic discriminant analysis   |            |
24 |      HDclassif   |         hdda   |                         High Dimensional Discriminant Analysis   |            |
25 |        HiDimDA   |         Mlda   |               Maximum Uncertainty Linear Discriminant Analysis   |            |
26 |        HiDimDA   |        RFlda   |                      Factor-Based Linear Discriminant Analysis   |            |
27 |          ipred   |         slda   |                        Stabilized Linear Discriminant Analysis   |            |
28 |          ipred   |    ipredbagg   |                                                    Bagged CART   |            |
29 |        kerndwd   |      kerndwd   |             linear and kernel distance weighted discrimination   |            |
30 |           klaR   |       loclda   |              Localized version of Linear Discriminant Analysis   |            |
31 |           KRLS   |     krlsPoly   |                    Polynomial Kernel Regularized Least Squares   |            |
32 |           KRLS   |         krls   |         Radial Basis Function Kernel Regularized Least Squares   |            |
33 |           laGP   |        newGP   |                                               Gaussian Process   |            |
34 |           lars   |         lars   |                                         Least Angle Regression   |            |
35 |        logicFS   |     logicBag   |                                        Bagged Logic Regression   |            |
36 |         mboost   |     gamboost   |                             Boosted Generalized Additive Model   |            |
37 |          mlegp   |        mlegp   |                                               Gaussian Process   |            |
38 |           mgcv   |          gam   |                       Generalized Additive Model using Splines   |            |
39 |           nnls   |         nnls   |                                     Non-Negative Least Squares   |            |
40 |   oblique.tree   | oblique.tree   |                                                  Oblique Trees   |            |
41 |        partDSA   |      partDSA   |  Partitioning Using Deletion, Substitution, and Addition Moves   |            |
42 |   penalizedLDA   | PenalizedLDA   |                         Penalized Linear Discriminant Analysis   |            |
43 |        plsRcox   |       coxpls   |                                   Cox-Model on PLSR components   |            |
44 |        plsRcox   |      coxpls2   |                                   Cox-Model on PLSR components   |            |
45 |        plsRcox   |      coxpls3   |                                   Cox-Model on PLSR components   |            |
46 |        plsRglm   |      plsRglm   |                Partial Least Squares Generalized Linear Models   |            |
47 |        probFDA   |         pfda   |                    Probabilistic Fisher discriminant analysis    |            |
48 |           qrnn   |         qrnn   |                             Quantile Regression Neural Network   |            |
49 | quantregForest   |          qrf   |                                         Quantile Random Forest   |            |
50 |   randomForest   |        parRF   |                                         Parallel Random Forest   |            |
51 |         relaxo   |       relaxo   |                                                  Relaxed Lasso   |            |
52 |       robustDA   |         rmda   |                           Robust Mixture Discriminant Analysis   |            |
53 |           rocc   |      tr.rocc   |                                           ROC-Based Classifier   |            |
54 |          rrcov   |       QdaCov   |                         Robust Quadratic Discriminant Analysis   |            |
55 |        rrcovHD   |       CSimca   |                                                          SIMCA   |            |
56 |        rrcovHD   |       RSimca   |                                                   Robust SIMCA   |            |
57 |            RRF   |          RRF   |                                      Regularized Random Forest   |            |
58 |            RRF   |    RRFglobal   |                                      Regularized Random Forest   |            |
59 |          RSNNS   |          rbf   |                                  Radial Basis Function Network   |            |
60 |          RSNNS   |       rbfDDA   |                                  Radial Basis Function Network   |            |
61 |          RWeka   |          LMT   |                                           Logistic Model Trees   |            |
62 |          RWeka   |           M5   |                                                     Model Tree   |            |
63 |          RWeka   |      M5Rules   |                                                    Model Rules   |            |
64 |           SDDA   |      sddaLDA   |                 Stepwise Diagonal Linear Discriminant Analysis   |            |
65 |           SDDA   |      sddaQDA   |              Stepwise Diagonal Quadratic Discriminant Analysis   |            |
66 |           sdwd   |         sdwd   |                        Sparse distance weighted discrimination   |            |
67 |            snn   |        mybnn   |                             Bagged Nearest Neighbor Classifier   |            |
68 |            snn   |        myknn   |                                  K Nearest Neighbor Classifier   |            |
69 |            snn   |       myownn   |                   Optimal Weighted Nearest Neighbor Classifier   |            |
70 |            snn   |        mysnn   |                         Stabilized Nearest Neighbor Classifier   |            |
71 |            snn   |        mywnn   |                           Weighted Nearest Neighbor Classifier   |            |
72 |      sparseLDA   |    sparseLDA   |                            Sparse Linear Discriminant Analysis   |            |
73 |           spls   |         spls   |                                   Sparse Partial Least Squares   |            |
74 |          stats   |          ppr   |                                  Projection Pursuit Regression   |            |
75 |        superpc   |      superpc   |                        Supervised Principal Component Analysis   |            |
76 |      sparsenet   |    sparsenet   |           Sparse linear regression with nonconvex optimization   |            |
77 |           sprm   |         prms   |                                    Partial robust M regression   |            |
78 |           sprm   |        sprms   |                             Sparse partial robust M regression   |            |
79 |           sprm   |        prmda   |                           Robust PLS for binary classification   |            |
80 |           sprm   |       sprmda   |                Sparse and robust PLS for binary classification   |            |
81 |           vbmp   |   vbmpRadial   |             Variational Bayesian Multinomial Probit Regression   |            |
82 |         LightGBM |      LightGBM  | [Gradient Boosting with tree based learners](https://github.com/Microsoft/LightGBM) |
83 |         glmmPQL |      MASS  | Generalized Linear Mixed Model |
84 |         gamm |      mgcv  | Generalized Additive Mixed Model |
85 |         maxent |      dismo  | Species Distribution Modeling |
86 |         maxnet |      maxnet  | Species Distribution Modeling |



# Speculative Enhancements

- These are ideas from the mlr community

 Number |      Idea     |   Ref Issue    |
:--|    :----------:  |:----------:
1 | Making a super learner from base learners applied to different parts of the data | [#153](https://github.com/mlr-org/mlr/issues/153)
2 | Sparse matrix support | [#453](https://github.com/mlr-org/mlr/issues/471)
3 | Score output for classes | [#355](https://github.com/mlr-org/mlr/issues/355)
4 | Smote for multiclass problems | [#905](https://github.com/mlr-org/mlr/issues/905)
5 | Impute wrappers for established packages | [#156](https://github.com/mlr-org/mlr/issues/156)
6 | Subsampling ensemble variance estimator | [#740](https://github.com/mlr-org/mlr/issues/740)
7 | Ordinal classification | [#852](https://github.com/mlr-org/mlr/issues/852)
8 | makeBaggingWrapper for survival tasks | [#877](https://github.com/mlr-org/mlr/issues/877)
9 | Unified logging | [#544](https://github.com/mlr-org/mlr/issues/544)
10 | Feature selection for clustering | [#541](https://github.com/mlr-org/mlr/issues/541)
11 | Confidence intervals in prediction | [#843](https://github.com/mlr-org/mlr/issues/843)
12 | Allow general model formulas | [#564](https://github.com/mlr-org/mlr/issues/564)
13 | Parallelization of underlying model fit | None
14 | Stability selection | None
15 | Method to create polynomial features | [#645](https://github.com/mlr-org/mlr/issues/645)
16 | Calibration and calibration slope | [#842](https://github.com/mlr-org/mlr/issues/842)
17 | Allow more stopping criteria for feature wrapper | [#104](https://github.com/mlr-org/mlr/issues/104)
18 | Use criterion function for feature selection | [#666](https://github.com/mlr-org/mlr/issues/666)
19 | Allow mandatory covariates during filtering/feature selection | [#170](https://github.com/mlr-org/mlr/issues/170)
20 | Submodel approaches | [#298](https://github.com/mlr-org/mlr/issues/298)
21 | Imbalanced and cost sensitive multiclass modeling | [#821](https://github.com/mlr-org/mlr/issues/821)
22 | Interaction detection methods | None
23 | Plotting undirected graph | None
24 | Plotting bar chart | None
25 | ROC plots for multi-label classification | [#641](https://github.com/mlr-org/mlr/pull/641)
26 | 3D plots | [#1161](https://github.com/mlr-org/mlr/pull/1161)

# mlr tutorial

## Logos

The mlr logos are stored at the following locations:

- `man/figures/logo_navbar.png`: Navbar + README logo (126x63 px)
- `man/figures/logo.png`: Favicon for website in black (32x32 px)

## Build instructions

With the start of v2.13 we switched from mkdocs to pkgdown. With this change, all source files are now located in this repo under vignettes/tutorial.

Modification of a tutorial section:

If you want to modify/add a tutorial section, please follow these steps:

Open the respective source file, e.g. task.Rmd.
Follow the style guide while editing:
Reference mlr functions as <function()>, e.g. makeLearner().
Reference external functions as package::function(), e.g. kernlab::ksvm().
Reference other tutorial pages with <name_of_vignette>.html, e.g. [bagging](bagging.html).
Always start a new sentence with a new line.
If you want to insert a paragraph, skip one line.
The most up-level header starts with ### and is subsequently followed by #### and ##### etc.
Always insert exactly one empty line before and after a code chunk, header, figure or a table.
Referencing images is a bit tricky since we need to ensure that they look good in both the HTML and PDF version. Put your image into vignettes/tutorial/devel/pdf/img/ and see the examples in resampling.Rmd, nested_resampling.Rmd or handling_of_spatial_data.Rmd.
Make sure that the .Rmd file is working on its own, i.e. compile it as a single file (preferably using build_article("tutorial/devel/<vignette-name>")) and see if everything works. Put required packages in the setup chunk at the beginning of the tutorial.
Rendering the tutorial locally:

If you want to view the complete pkgdown site locally, run pkgdown::build_site(lazy = TRUE). You don't have to render the complete site every time you change one tutorial. The lazy = TRUE argument ensures that only pages are rebuilt that have changed. Also, if you have built the whole site once, you can just build the vignettes again by using build_articles(lazy = TRUE). More specific, if you are working on one vignette, you can run build_article("tutorial/devel/<vignette-name>"). You do not need to pass the .Rmd extension when using build_article().

Important: Do not commit any file in docs/ as the rendering will be done by Travis!

Adding a new section:

Edit _pkgdown.yml and add the new section at the appropriate place.

Issues and Pull Requests:

If you want to open an issue or pull request that is related to mlr-tutorial, label it with tutorial and mention jakob-r or pat-s if you need help.
