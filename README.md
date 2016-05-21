# ExcelReaders

[![Build Status](https://travis-ci.org/davidanthoff/ExcelReaders.jl.svg?branch=master)](https://travis-ci.org/davidanthoff/ExcelReaders.jl)
[![Build status](https://ci.appveyor.com/api/projects/status/n8039pvotidkussq/branch/master?svg=true)](https://ci.appveyor.com/project/davidanthoff/excelreaders-jl/branch/master)
[![Coverage Status](https://coveralls.io/repos/davidanthoff/ExcelReaders.jl/badge.svg)](https://coveralls.io/r/davidanthoff/ExcelReaders.jl)
[![ExcelReaders](http://pkg.julialang.org/badges/ExcelReaders_0.3.svg)](http://pkg.julialang.org/?pkg=ExcelReaders&ver=0.3)
[![ExcelReaders](http://pkg.julialang.org/badges/ExcelReaders_0.4.svg)](http://pkg.julialang.org/?pkg=ExcelReaders&ver=0.4)

ExcelReaders is a package that provides functionality to read Excel files.

## Installation

Use ``Pkg.add("ExcelReaders")`` in Julia to install ExcelReaders and its dependencies.

The package uses the Python xlrd library. If either Python or the xlrd package are not installed on your system, the package will use the [conda.jl](https://github.com/Luthaf/Conda.jl) package to install all necessary dependencies automatically.

## Alternatives

The [Taro](https://github.com/aviks/Taro.jl) package also provides Excel file reading functionality. The main difference between the two packages (in terms of Excel functionality) is that ExcelReaders uses the Python package [xlrd](https://github.com/python-excel/xlrd) for its processing, whereas Taro uses the Java packages Apache [Tika](http://tika.apache.org/) and Apache [POI](http://poi.apache.org/).

## Basic usage

The most basic usage is this:

````julia
using ExcelReaders

data = readxl("Filename.xlsx", "Sheet1!A1:C4")
````

This will return a ``DataMatrix{Any}`` with all the data in the cell range A1 to C4 on Sheet1 in the Excel file Filename.xlsx.

If you expect to read multiple ranges from the same Excel file you can get much better performance by opening the Excel file only once:

````julia
using ExcelReaders

f = openxl("Filename.xlsx")

data1 = readxl(f, "Sheet1!A1:C4")
data2 = readxl(f, "Sheet2!B4:F10")
````

## Reading a whole sheet

The ``readxlsheet`` function reads complete Excel sheets, without a need to specify precise range information. The most basic usage is

````julia
using ExcelReaders

data = readxlsheet("Filename.xlsx", "Sheet1")
````

This will read all content on Sheet1 in the file Filename.xlsx. Eventual blank rows and columns at the top and left are skipped. ``readxlsheet`` takes a number of optional keyword arguments:

- ``skipstartrows`` accepts either ``:blanks`` (default) or a positive integer. With ``:blank`` any empty initial rows are skipped. An integer skips as many rows as specified.
- ``skipstartcols`` accepts either ``:blanks`` (default) or a positive integer. With ``:blank`` any empty initial columns are skipped. An integer skips as many columns as specified.
- ``nrows`` accepts either ``:all`` (default) or a positive integer. With ``:all``, all rows (except skipped ones) are read. An integer specifies the exact number of rows to be read.
- ``ncols`` accepts either ``:all`` (default) or a postiive integer. With ``:all``, all columns (except skipped ones) are read. An integer specifies the exact number of columns to be read.

``readxlsheet`` also accepts an ExcelFile (as obtained from ``openxl``) as its first argument.

## Reading into a DataFrame

To read into a DataFrame:

````julia
using ExcelReaders
using DataFrames

df = readxl(DataFrame, "Filename.xlsx", "Sheet1!A1:C4")
````

This code will use the first row in the range A1:C4 as the column names in the DataFrame.

To read in data without a header row use

````julia
df = readxl(DataFrame, "Filename.xlsx", "Sheet1!A1:C4", header=false)
````

This will auto-generate column names. Alternatively you can specify your own names:

````julia
df = readxl(DataFrame, "Filename.xlsx", "Sheet1!A1:C4",
            header=false, colnames=[:name1, :name2, :name3])
````

You can also combine ``header=true`` and a custom ``colnames`` list, in that case the first row in the specified range will just be skipped.

To read the whole sheet into a DataFrame (respective keyword arguments (`header`, `skipstartrows` etc.) should work as expected):

```julia
df = readxlsheet(DataFrame, "Filename.xlsx", "Sheet1")
```
