# ROMP

## R openMP API
* R Syntax to Fortran Converter
* Accelerate R code by compilation
* Parallelize R code by vectorization
* Speedup by Compilation: ~100
* Speedup by Vectorization: ~100
* Total Speedup: ~10000

* ROMP scales up to ~100 cores (SMP)
* Acceleration up to several 10000
* Pre Alpha Version
* Combination Rmpi+ROMP?
* Extending map/reduce: Use monads?
* Type inference aka automatic typing?

* Use functional programming style
* Use closures
* R functions to Fortran functions in the contains part.
* Higher order functions: map/reduce
* Translate map/reduce to openMP for/reduce pragmas

* R functions are translated to pure functions in Fortran
* R sum is replaced by sum.mp
* R apply is replaced by apply.mp
* Typing required, allowed types: int(), dbl()
## Example

### Compute distance of two time series
* pure R code: x = as.double(runif(100)) y = as.double(runif(100)) for(i in 1:100) res=res+(x[i]-y[i])**2

the summation can also be written as: res=sum((x-y)**2)

ROMP code: 
```R 
sum.mp(dosum,(x[i]-y[i])**2, dbl(),i=1:100)
dosum.f <- compile.mp(dosum(),dbl(),x=dbl(100),y=dbl(100))
dosum.f(res=res, x=x, y=y) 
```
# Examples

We want to compute the pointwise dimension of a point cloud of np points in ndim dimensions. The locations of the points are given by a two-dimensional array x

Ref: Local Scaling Properties for Diagnostic Purposes by W. Bunk, F. Jamitzky, R. Pompl, C. Rath and G. Morfill, Springer 2002

# Pure R implementation

The local number density at each point within a radius r is then computed by the following pure R code: 
```R
dist <- function(i,j,x,r) ifelse(sum((x[i,1:ndim]-x[j,1:ndim])**2)>r**2,0,1)
dens_one <- function(j,x,r) sum(sapply(1:np, function(i) dist(i,j,x,r)))
comp.dens <- function(x,r) sapply(1:np, function(j) dens_one(j,x,r))
comp.dens(x, r=0.1) 
```
The function dist computes whether two points of a point cloud x[np,ndim] are closer that a distance r and returns 0 or 1 respectively.
# Romp implementation
```R
sum.mp(dens_one,ifelse(sum((x[i,1:ndim]-x[j,1:ndim])**2)>r**2,0,1), int(), i=1:np, j=int())
apply.mp(dens, dens_one(j), int(np), j=1:np)
comp.dens <-compile.mp( dens(),int(np),x=dbl(np,ndim),r=dbl(),ndim=int(),np=int())
comp.dens(x, r=0.1, ndim=3, np=100000) 
```
# Results
By running Romp on an SGI Altix we obtained the following numbers:

* Pure R: time = 21800s = 6h
* Romp, nproc=1 time = 3.2s
* Romp, nproc=8 time = 0.6s
