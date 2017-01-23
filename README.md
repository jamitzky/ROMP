# ROMP
OpenMP Bindings for R

Introduction

ROMP

R openMP API
R Syntax to Fortran Converter
Accelerate R code by compilation
Parallelize R code by vectorization
Speedup by Compilation: ~100
Speedup by Vectorization: ~100
Total Speedup: ~10000
Summary and Outlook

ROMP scales up to ~100 cores (SMP)
Acceleration up to several 10000
Pre Alpha Version
Combination Rmpi+ROMP?
Extending map/reduce: Use monads?
Type inference aka automatic typing?
Philosophy

Use functional programming style
Use closures
R functions to Fortran functions in the contains part.
Higher order functions: map/reduce
Translate map/reduce to openMP for/reduce pragmas
Abstractions

R functions are translated to pure functions in Fortran
R sum is replaced by sum.mp
R apply is replaced by apply.mp
Typing required, allowed types: int(), dbl()
Example

Compute distance of two time series
pure R code: x = as.double(runif(100)) y = as.double(runif(100)) for(i in 1:100) res=res+(x[i]-y[i])**2

the summation can also be written as: res=sum((x-y)**2)

ROMP code: ``` sum.mp(dosum,(x[i]-y[i])**2, dbl(),i=1:100)

dosum.f = compile.mp(dosum(),dbl(),x=dbl(100),y=dbl(100))

dosum.f(res=res, x=x, y=y) ```

wiki syntax: http://code.google.com/p/support/wiki/WikiSyntax
