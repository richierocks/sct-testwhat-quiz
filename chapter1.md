---
title       : SCT quiz
description : 
attachments :
  slides_link : https://s3.amazonaws.com/assets.datacamp.com/course/teach/slides_example.pdf

--- type:NormalExercise lang:r xp:100 skills:1 key:31ea872ce2
## Simple tests (1)

#### pass 1

```
library('zoo')

x <- zoo(1:3)

print(x)
```

#### pass 2

```
library(zoo)

x <- y <- zoo(1:3)

print(y)
```

#### fail 1 - "x is not correct"

```
library(zoo)

x = c(1,2,3)

print(x)
```

#### fail 2 - "x is undefined"

```
library(zoo)
```

#### fail 3 - "Expected same output as `zoo(1:3)`"

```
library(zoo)

x <- zoo(1:3)
```


*** =pre_exercise_code
```{r}

```

*** =sample_code
```{r}
library(zoo)

x <- zoo(1:3)

print(x)
```

*** =solution
```{r}
library(zoo)

x <- zoo(1:3)

print(x)
```

*** =sct
```{r}
test_object("x")
test_output_contains(
  "print(x)",
  incorrect_msg = "Expected same output as `zoo(1:3)`"
)
```

--- type:NormalExercise lang:r xp:100 skills:1 key:86f183628a
## Simple tests (2)


#### pass 1

```
f("blue")
```

#### pass 2

```
f( "b" )
```

#### pass 3

```
f('b')
```

#### pass 4
```
(f("b"))
```

#### fail 1 - "should call f('blue') or f('b')"

```
f("blueberry")
```

#### fail 2 - "should cal f('blue') or f('b')"

```
pf("blue")
```


*** =pre_exercise_code
```{r}
f <- function(x) NULL
pf <- function(x) NULL
```

*** =sample_code
```{r}
f("b")
```

*** =solution
```{r}
f("b")
```

*** =sct
```{r}
# Use only a check_code SCT
# Generated with rebus
# "f" %R% 
#   OPEN_PAREN %R% 
#   space(0, Inf) %R% 
#   "['\"]" %R% 
#   "b" %R% 
#   optional("lue") %R% 
#   "['\"]" %R% 
#   space(0, Inf) %R% 
#   CLOSE_PAREN %>%
#   print(encode_string = TRUE)
rx <- "f\\([[:space:]]*['\"]b[lue]?['\"][[:space:]]*\\)"

# Ignoring typo in "should cal f('blue') or f('b')", and editing msg to make code in markdown
ex() %>% check_code(rx, missing_msg = "should call `f('blue')` or `f('b')`")

```

--- type:NormalExercise lang:r xp:100 skills:1 key:69cbf4cfc4
## Control Tests (1)

#### pass

```
for (jj in 1:10) if (jj > 1) jj else 0
```

#### fail 1 - "bad cond test"

```
for (jj in 1) if (jj > 1) jj else 0
```

#### fail 2 - "no inner if"

```
for (ii in 1:10) ii
```

#### fail 3 - "bad if cond"

```
for (ii in 1:10) if (ii > 1000) ii else 0
```

#### fail 4 - "no else cond"

```
for (ii in 1:10) if (ii > 1) ii
```

*** =pre_exercise_code
```{r}

```

*** =sample_code
```{r}
for (ii in 1:10) if (ii > 1) ii else 0
```

*** =solution
```{r}
for (ii in 1:10) if (ii > 1) ii else 0
```

*** =sct
```{r}
test_for_loop(
  cond_test = {
    # This follows the pattern in example(test_control)
    # It will still fail, for, e.g., for(1:10 in ii)
    # Ideally I want to examine 
    # quote(for (jj in 1:10) if (jj > 1) jj else 0) %>% as.list()
    # But don't know how to do this
    bad_for_cond_msg <- "bad cond test"
    test_student_typed("in", not_typed_msg = bad_for_cond_msg)
    test_student_typed("1", not_typed_msg = bad_for_cond_msg)
    test_student_typed("10", not_typed_msg = bad_for_cond_msg)
  },
  expr_test = {
    test_if_else(
      1,
      if_cond_test = {
        bad_if_cond_msg <- "bad if cond"
        # Match > 1 or < 1 with optional space, and maybe integer (1L)
        test_student_typed("(?:> *1|1L? *<)", not_typed_msg = bad_if_cond_msg, fixed = FALSE)
      },
      if_expr_test = {
        # Answer should just be the variable name in the condition, but I don't
        # know how to match this variable name to the one in the condition block
        # Regex is a very rough approximation to a valid R variable name
        test_student_typed("[a-zA-Z.]+[a-zA-Z0-9._]+")
      },
      else_expr_test = {
        # Match 0 or 0L or 0.0000 but not 0.00000001
        test_student_typed("0(L|(?:\\.0+))? *$", fixed = FALSE)
      },
      not_found_msg = "no inner if",
      missing_else_msg = "no else cond"
    )
  }
)
```

--- type:NormalExercise lang:r xp:100 skills:1 key:72a629a74f
## Data Objects

#### pass

```
d <- transform(d, ttl = x + y)
```

#### fail 1 - "d is undefined"

```
rm('d')
```

#### fail 2 - "bad value for ttl"

```
d$ttl <- d$x
```

#### fail 3 - "missing col y"

```
d$ttl <- d$x + d$y
d$y <- NULL
```

*** =pre_exercise_code
```{r}
d <- data.frame(x=1:10, y=11:20)
```

*** =sample_code
```{r}
d$ttl <- d$x + d$y
```

*** =solution
```{r}
d$ttl <- d$x + d$y
```

*** =sct
```{r}
# Want to type test_data_frame("d"), however
# can't use test_data_frame() since it doesn't give enough 
# flexibility when overriding err msgs

# Also, check_column() doesn't seem to like being piped to another check_column()
# so having to repeat the check_object()
ex() %>%
  check_object("d", undefined_msg = "`d` is undefined") %>%
  check_column("ttl", col_missing_msg = "missing col `ttl`") %>%
  check_equal(incorrect_msg = "bad value for `ttl`")
ex() %>%
  check_object("d", undefined_msg = "`d` is undefined") %>%
  check_column("x", col_missing_msg = "missing col `x`") %>%
  check_equal(incorrect_msg = "bad value for `x`")
ex() %>%
  check_object("d", undefined_msg = "`d` is undefined") %>%
  check_column("y", col_missing_msg = "missing col `y`") %>%
  check_equal(incorrect_msg = "bad value for `y`")
```

--- type:NormalExercise lang:r xp:100 skills:1 key:631d01c691
## Function Definition

#### pass 1

```
f <- function(x, y, z=1) sum(x + y, z)
```

#### fail 1 - "missing 3rd arg"

```
f <- function(a, b) sum(a + b)
```

#### fail 2 - "no + operator"

```
f <- function(a, b, c=1) sum(a,b,c)
```

#### fail 3 - "no sum call"

```
f <- function(a, b, c=1) a + b + c
```

*** =instructions

*** =hint

*** =pre_exercise_code
```{r}

```

*** =sample_code
```{r}
f <- function(a, b, c=1) sum(a + b, c)
```

*** =solution
```{r}
f <- function(a, b, c=1) sum(a + b, c)
```

*** =sct
```{r}
test_object("f", eval = FALSE)

# Check three args
# This is probably superfluous, since test_function_definition has 
# incorrect_number_arguments_msg arg, but I want this test before the one of
# formal arg defaults
test_expression_result(
  "length(formalArgs(f))",
  incorrect_msg = "`f` should have 3 arguments; are you missing the 3rd arg?"
)

# Check args have correct defaults; don't care what they are named
test_expression_result(
  "unname(formals(f))",
  incorrect_msg = "The formal arguments of `f` are not correct"
)

test_function_definition(
  "f",
  function_test = {
    # Do it add up OK?
    test_expression_result("f(1.2345, -999.99)")
    # Should probably test it works with complex inputs too
    test_expression_result("f(55354 + 6236i, 967896, -12342453245)")
    # Need to test a vector where + will recycle, in order to distinguish
    # from a simple sum(a, b, c)
    test_expression_result("f(1:4, 1:2)")
  },
  body_test = {
    # Is + called?
    test_student_typed("+", incorrect_msg = "no + operator")
    # Is sum called?
    test_student_typed("sum(", incorrect_msg = "no sum call")
  }
)
```

--- type:NormalExercise lang:r xp:100 skills:1 key:7839a0890c
## Function Call

#### pass 1 

```
lm(d$x ~ d$y)
```

#### pass 2

```
with(d, lm(x ~ y))
```

*** =pre_exercise_code
```{r}
d <- data.frame(x = 1:10, y = 11:20)
```

*** =sample_code
```{r}
lm(x ~ y, data=d)
```

*** =solution
```{r}
lm(x ~ y, data=d)
```

*** =sct
```{r}
# Test that lm() is called, but don't evaluate args, since there are different ways of specifying them
test_function(
  "lm",
  args = "formula", # don't enforce use of data arg
  eval = NA
)
# Can't use test_output_contains("lm(x ~ y, data=d)") either
# since lm prints the function call, which may vary
ex() %>% check_output("Call:", fixed = TRUE)
ex() %>% check_output("Coefficients:", fixed = TRUE)
ex() %>% check_output("\\(Intercept\\) +(?:d\\$)?y")
ex() %>% check_output("-10 +1")
```


--- type:NormalExercise lang:r xp:100 skills:1 key:f033369188
## Function Call (2)

#### pass 1 - 

```
lapply(df, function(col) sum(col))
```

#### fail 1 - "missing first pos arg"

```
lapply(FUN=sum)
```

#### fail 2 - "incorrect first pos arg"

```
lapply(sum)
```

#### fail 3 - "missing second pos arg"

```
lapply(df)
```


*** =pre_exercise_code
```{r}
df <- data.frame(a = 1:10, b = 11:20)
```

*** =sample_code
```{r}
lapply(df, sum)
```

*** =solution
```{r}
lapply(df, sum)
```

*** =sct
```{r}
test_function(
  "lapply",
  args = c("X", "FUN"),
  eval = c(TRUE, NA),
  args_not_specified_msg = c(
    "missing first pos arg",
    "missing second pos arg"
  ),
  incorrect_msg = c(
    "incorrect first pos arg",
    "incorrect second pos arg"
  )
)
test_output_contains("lapply(df, sum)")
```
--- type:NormalExercise lang:r xp:100 skills:1 key:2564475b6e
## Logic Test

#### pass - 

```
x <- mean(1:3)
lkjsdf
```

#### fail 1 - "error in code"

```
x <- men(1:3)
```

#### fail 2  - "undefined x"

```
```

#### fail 3 - "did you call mean?"

```
x <- 2
```


*** =instructions

*** =hint

*** =pre_exercise_code
```{r}

```

*** =sample_code
```{r}
x <- mean(1:3)
```

*** =solution
```{r}
x <- mean(1:3)
```

*** =sct
```{r}
# Don't know how to check for errors in code, and yet somehow allow student to type nonsense after the solution
# Currently fails the lksjdfsjf test
test_error(incorrect_msg = "error in code")
test_object("x", undefined_msg = "undefined x")
test_function(
  "mean",
  args = "x",
  not_called_msg = "did you call mean?"
)
```

--- type:NormalExercise lang:r xp:100 skills:1 key:f02f67218b
## Backend Trouble

Use a testwhat function in the pre\_exercise\_code, to allow the solution to pass.

*** =pre_exercise_code
```{r}
allow_solution_error()
```

*** =sample_code
```{r}
f <- function(a) stopifnot(a > 5)
f(3)
```

*** =solution
```{r}
f <- function(a) stopifnot(a > 5)
f(3)
```

*** =sct
```{r}

```

