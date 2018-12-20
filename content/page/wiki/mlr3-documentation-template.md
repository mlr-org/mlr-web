---
date: 2018-12-19T08:00:00+06:00
title: mlr3 documentation template
authors: ["patrick-schratz"]
slug: mlr-documentation-template
menu:
   main:
      parent: wiki
weight: -1
toc: false
---

# mlr3 documentation template

## Rules

* The "Usage" section is listed as follows (both in usage and details). In the subsection (e.g. members), we list alphabetically.
  1. Constructor
  1. Members / ABs
  1. Methods
* We use `@family"` to group stuff together.
* `“Inherits from [TheSuperClass]”` is the first line in "Usage".
* Methods in "Usage" can be subgrouped in “Accessors” und “Mutators”
* Section "Details" is ordered as follows. In the subsection (e.g. members), we list alphabetically.
  1. Members
  1. Methods
* Linking to functions (both internal ones and external ones) is done without brackets, e.g. `[data.table::data.table]`.
* The link to the rendered pkgdown HTML help is added with the tag `@references` and the following structure: `[HTML help page](https://<package name>.mlr-org.com/reference/<name>.html)`
* The `@family` string can contain spaces. The string generated should make a sentence when inserted in "Other <family>:".
* One @family should group all classes that inherit from the base class (including the base class). Multiple @family sections are possible.
* `@keywords internal `: Function/Class is exported but mostly for internal use.

## Example documentation

```r
#' @title ClassFoo
#' @format [R6Class] object
#' @description 
#' This is the description of class foo.
#' @section Usage:
#' Inherits from [TheSuperClass]
#'
#' ```
#' # Construction
#' f = ClassFoo$new(argument1, argument2)
#'
#' # Members
#' f$member1
#' f$member2
#'
#' # Methods
#' f$method1(argument3 = 123)
#' f$method2()
#' ```
#' 
#' @section Arguments:
#' * `argument1` ([R6 class] or `type`):\cr
#'   Description of argument1
#’
#' @section Details:
#' * `member_var1`: Description of member_var1. 
#' * `member_var2`: Description of member_var2. 
#' * `method1()`: Description of method1. 
#' * `method2()`: Description of method2. 
#'
#' @family NameOfBaseClass
#' @family Objects To Handle Stuff
#' @references [HTML help page](https://<pkgname>.mlr-org.com/reference/<.Rd file name>.html
#'
#'
#' @examples
#' f = ClassFoo$new("hello", 123)
#' f$method1(10)
NULL
```
