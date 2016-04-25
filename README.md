
# AutoAligns

[![Build Status](https://travis-ci.org/tpapp/AutoAligns.jl.svg?branch=master)](https://travis-ci.org/tpapp/AutoAligns.jl)

This package helps align text for printing with a monospace font, by keeping track of the maximum width of each column. It is useful for printing matrices, tables, and analogous structures, and in particular writing methods for `Base.print`.

## Usage

Create an `AutoAlign` object, then use `print` to save values into it, which are strings or converted to strings. The `AutoAlign` object keeps track of the current column (which you can query with `position`, but that is rarely necessary), and you can also provide a default alignment, or specify it before printing, using the methods
```{julia,eval=false}
print(aa::AutoAlign, xs...)
println(aa::AutoAlign, xs...)
```
where the latter starts a new line after saving the values. Both accept a keyword argument `align`, for specifying alternate alignent for the preceding arguments.

Alignment can be specified as follows:

1. an atom `left`, `right`, or `center`, or any other user extension which is a subtype of `Alignment`,
2. a vector of alignment specifiers: for positions outside the length of the vector, the last element is used,
3. a `Dict` of `position => alignment` pairs, where the position `:default` provides the alignment for items not in the `Dict`.

## Example

````julia
julia> using AutoAligns

julia> let aa = AutoAlign(align = Dict(1 => left, :default => right))
    for (i,r) in zip([1,100,10000],["a","bb","ccc"])
        print(aa, r)
        for j in 1:5
            print(aa, " ", i+j)
        end
    end
    print(aa)
end

````





## Notes

Spaces or separators such as `|` are not treated in a special way, and therefore should be accounted for when specifying positions for alignment.

Printing an `AutoAlign` object to a stream pads the strings with spaces so as to achieve the desired alignment.

An `AutoAlign` object is not a subtype of `IO`, as it does not implement a `writewrite(::AutoAlign, x::UInt8)` method. Unlike `IOBuffer`, printing does not delete the contents.

Custom alignment types can be defined as subtypes of `Alignment`, by implementing `align_string`.

