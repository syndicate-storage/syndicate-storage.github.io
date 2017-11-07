---
layout: post
title: Documenting Syndicate with Doxygen
---

---

Doxygen is a tool for writing software reference documentation (a.k.a. a documentation generator). Because the documentation is written within code, it is relatively easy to keep up to date.  For this reason, the Syndicate project utilizes Doxygen to generate code and tool related documentation in the form of a web page located at [https://butler.opencloud.cs.arizona.edu/docs/](https://butler.opencloud.cs.arizona.edu/docs/) and man pages that are distributed with the Syndicate installation packages.  Utilizing the [syndicate-docs](https://github.com/syndicate-storage/syndicate-docs) repo, Syndicate documentation is auto-generated via Syndicate's [Jenkins](https://butler.opencloud.cs.arizona.edu/jenkins) server.  Information on how to install and configure Doxygen from scratch is located at the end of this document.


## Code Documentation

### Comment Blocks

Doxygen accepts multiple comment block styles (e.g. JavaDoc, C++, Qt, etc.).  For multi-line comment blocks, JavaDoc seems to be the most commonly used and suggested.

```
/**
 * ... the intermediate *'s are optional, but suggested for consistency...
 */
```
For single line documentation blocks, the C++ style is most commonly used.

```
/// ...text
``` 

or using an inline comment

```
variable ///< ...text about variable
```

Typically, developers choose to solely use these two styles for the sake of consistency.  Furthermore, comment blocks are often written in header files before the declaration it describes (with the same indentation level).  But since much of the original syndicate code and functionality was described/documented in the cpp files, and most of the structures are defined in the cpp files rather than the header files, in many cases it may make more sense to primarily add Doxygen documentation in the cpp files.

---
###### Other Supported comment block styles include...

Psuedo C++ style

```
///
/// ... text, the blank comment below ends the block
///
```
```
//!
//! ... text, the blank comment below ends the block
//!
```
```
/////////////////////////////////////////////////
/// ... text ...
/////////////////////////////////////////////////
```
```
/********************************************//**
 *  ... text, requires 2 slashes shown above
 ***********************************************/
```

or Qt style

```
/*!
 * ... intermediate *'s are optional ...
 */
```

### Common Tags / Special Commands

Tags or special commands define documentation instructions within a comment (or comment block) for Doxygen.  For consistency purposes, these should begin with the `@` symbol instead of using `\`. Below is a list of commonly used tags / special commands, see
[https://www.stack.nl/~dimitri/doxygen/manual/commands.html](https://www.stack.nl/~dimitri/doxygen/manual/commands.html) for more examples.


#### @brief

```
/**
 * @brief This is a brief description
 *        about something.
 *
 * And this should be the detailed description.
 * Notice the blank line between indicates the end of the brief description.
 */
```

#### @details
A detailed description.  Usually defined after the *_@brief_* section.  Using @details is optional, it can be skipped entirely or a detailed description can be designated by not specifying a special command, as seen in the example above.

#### @param
Indicates a parameter description as follows.  The in and out designation is specified with `[in]`, `[out]`, and `[in,out]` for both.

```
/**
 * Copies bytes from a source memory area to a destination memory area,
 * where both areas may not overlap.
 * @param[out] dest The memory area to copy to.
 * @param[in]  src  The memory area to copy from.
 * @param[in]  n    The number of bytes to copy
 */
void memcpy(void *dest, const void *src, size_t n);
```

#### @tparam
Similar to @param, except use for a class or function template parameter.

#### @return
Describes the return value

#### @retval
Similar to @return but useful when multiple specific values are expected

```
/**
 * @brief A useful function
 * 
 * @retval 0 Success
 * @retval -EINVAL Invalid commands
 * @retval -ENOMEM Out of memory
 * @retval -EBADF No file descriptor
 */
```

#### @class

`@class <name> [<header-file>] [<header-name>]`

Indicates that a comment block contains documentation for a class with name <name>. A header file and a header name should be specified, but is optional.

Example:

```
/** @class Test class.h "inc/class.h"
 *  @brief This is a test class.
 *
 * Some details about the Test class.
 */
class Test
{
};
```

#### @attention

Describes a message that needs attention.

####@note
Describes an additional note.  This has the affect of indenting the paragraph.

#### @ref

Creates a reference to a named section, subsection, page or anchor.

```
* @ref syndicate-cat  //take note this is a generated "page", not a file (i.e. not a cpp/h file) or function.
```

#### @see
Cross reference to related code documentation (a.k.a. "see also" or `@sa`)

```
/**
 * @brief Compute an element-wise cosine.
 *
 * @see sin
 * @see tan
 * @see [numpy.vectorize](https://docs.scipy.org/doc/numpy/reference/generated/numpy.vectorize.html)
 */
vector<double> cos(vector<double> const & angles);
```

#### @relatesalso
This is good for operator functions, since they are related to another function.

```
/**
 * Add two images pixel-by-pixel.
 *
 * @relatesalso ImageF
 */
ImageF operator+(ImageF const & lhs, ImageF const & rhs);
```

#### @throws

Same as @throw or @exception, but use @throws to be consistent

```
/**
 * Write an image to disk.
 *
 * @throws IoError Thrown if `fileName` could not be written to.
 */
void writeImage(std::string const & fileName);
```

#### @todo

Describe a section that needs to be finished.  The description is also added to a TODO list that can be referenced separately.

#### @overload
Generate standard text for an overloaded function.  Also see example output [http://www.stack.nl/~dimitri/doxygen/manual/examples/overload/html/class_test.html](http://www.stack.nl/~dimitri/doxygen/manual/examples/overload/html/class_test.html)  Take note that the \a located in the description below tells Doxygen to italicize the word after it.

```
/** @class Overload_Test
 *  @brief A short description.
 *   
 *  More text.
 */
class Overload_Test 
{
  public:
    void drawRect(int,int,int,int);
    void drawRect(const Rect &r);
};

/**
 * @brief Draw a rectangle
 * 
 * This command draws a rectangle with a left upper corner at ( \a x , \a y ),
 * width \a w and height \a h.
 */
void Overload_Test::drawRect(int x,int y,int w,int h) {}

/**
 * @overload void Overload_Test::drawRect(const Rect &r)
 */
void Overload_Test::drawRect(const Rect &r) {}

```

#### @copydetails

Copy the details section from another comment block.  This is useful when multiple comment blocks have identical comments.

```
@copydetails some_function_name_that_has_a_comment_block()
```

#### @image
@image \<format> \<file> ["caption"] [\<sizeindication>=\<size>]

Inserts an image into the documentation. This command is format specific, so if you want to insert an image for more than one format you'll have to repeat this command for each format.

```
/** Here is a snapshot of my new application:
 *  @image html application.jpg
 *  @image latex application.eps "My application" width=10cm
 */
```

#### @section

Define your own section

```
@section example EXAMPLES
```
---

###File and page related commands

#### @file

Indicates that a comment block contains documentation for a source or header file with name <name>.  Duplicate file names should also include a unique path, for example, 'libsyndicate/opts.h'

```
/**
 * @file syndicate-cat.cpp
 */
```

#### @author

Specify the authors name.  Usually accompanied with @file and @date 

#### @date

Provide the date that the file was created  Usually accompanied with @file and @author.

```
 @date 9 Mar 2016
```

#### @page

Indicates that a comment block contains a piece of documentation that is not directly related to one specific class, file or member.  This has the effect of creating a "related page" in the web page produced by Doxygen and creating a *__man page__* that is not specific to a file.  For example, `@page syndicate-cat` will create a page associated with _syndicate-cat_ instead of the syndicate-cat.cpp/h files.

---
### Example Function

```
/**
 * @brief Read an image from disk.
 *
 * @param[in] fileName The file to read. Must be either absolute or relative to the program working directory.
 *
 * @return the double image stored in `fileName`.
 *
 * @throws IoError Thrown if `fileName` does not exist or is not readable.
 */
lsst::afw::image::Image<double> loadImage(std::string const & fileName);
```
 

### Inline comments

```
/**
 * @brief Supported shapes
 *
 * These are the supported shapes for this simple enum
 */
enum class myshapeenum
{
	square,  ///< this is a square, notice the "<"
	triangle ///< this is a triangle
}
```

### Example cpp file (using _@file_)

```
/**
 * @file syndicate-cat.cpp
 * @brief Contains main() function (i.e. entry point) for the syndicate-cat tool
 *
 * @see syndicate-cat.h,
 * @ref syndicate-cat
 *
 * @author Jude Nelson
 */
```

### Example header file (using _@file_)

```
// file documentation
/**
 * @file syndicate-cat.h
 * @author Jude Nelson
 * @date 9 Mar 2016
 *
 * @brief syndicate-cat header file
 *
 * @author Jude Nelson
 *
 * @see syndicate-cat.cpp,
 * @ref syndicate-cat
 */
```

### Example man page (using _@page_)

The comment block below should follow the _header_ comment block shown above, since the header file will contain documentation for both the header file (using _@file_) and the associated man page.<br>
Man page comment blocks use the _@section_ command to define man page sections, since typically man pages use ALL-CAPS to indicate section names (e.g. SYNOPSIS, DESCRIPTION, EXAMPLES, AUTHOR...)

```
// man page and related pages documentation
/**
 * @page syndicate-cat
 * @brief Concatenate files to standard output
 *
 * @section synopsis SYNOPSIS
 * syndicate-cat -u USERNAME -v VOLUME_NAME -g GATEWAY_NAME [OPTION]... /FILE...
 *
 * @section description DESCRIPTION
 * Concatenate FILE(s) in syndicate and print on the standard output.
 *
 * @copydetails md_common_usage()
 *
 * @section example EXAMPLES
 * syndicate-cat -u syndicate@example.com -v syndicate_volume -g syndicate_gateway -d2 -f -c "syndicate.conf" /file1
 *
 * @section author AUTHOR
 * Written by Jude Nelson
 *
 * @section bugs REPORTING BUGS
 * Online help is available at http://www.syndicate-storage.org
 *
 * @section copyright COPYRIGHT
 *
 * @copydetails md_print_copywrite()
 *
 * @copydetails md_print_license()
 *
 * @section see SEE ALSO
 * syndicate-cat.cpp(3)
 * syndicate-cat.h(3)
 */
```

## Groups

Doxygen allows for the ability to organize or group things together such as files, namespaces, classes, functions, variables, enums, typedefs, and defines, but also other groups.  This is useful, for example, if a set of classes were all related to a specific capability.  The group will be categorized as modules or submodules in the webpage that Doxygen produces. For a more thorough explanation, see [http://www.stack.nl/~dimitri/doxygen/manual/grouping.html](http://www.stack.nl/~dimitri/doxygen/manual/grouping.html)

## Using Doxygen to Document Python

Doxygen is compatible with Python, but there are differences is in how the comment blocks are formatted.  In Python, Doxygen natively uses the `#` symbol to detect comment blocks.  The first line of the comment block should begin with `##`.

```
## @brief The brief description
#
# The detailed description
```
Alternatively, installing *doxypypy* enables Doxygen to use docstrings (i.e. `""" ... """`) and slightly more human-readable formatting.  For example, with *doxypypy* the docstring can be located in the function, and looks like the following.  For Syndicate, doxypypy is already configured, therefore the docstrings format is compatible.
```
def myfunction(arg1, arg2, kwarg='whatever.'):
    """
    Does nothing more than demonstrate syntax.

    This is an example of how a Pythonic human-readable docstring can get parsed by doxypypy and marked up with Doxygen commands as a regular input filter to Doxygen.

    Args:
        arg1:   A positional argument.
        arg2:   Another positional argument.

    Kwargs:
        kwarg:  A keyword argument.

    Returns:
        A string holding the result.

    Raises:
        ZeroDivisionError, AssertionError, & ValueError.

    Examples:
        >>> myfunction(2, 3)
        '5 - 0, whatever.'
        >>> myfunction(5, 0, 'oops.')
        Traceback (most recent call last):
      		...
    """
```
###### Possible Issues

Utilizing doxypypy is an attractive option for python, however it does create the adverse affect of sometimes creating Doxygen-based documentation of sections that are not intented to be documented.  For example, having the copyright section located in a docstring at the beginning of a file would likely tell Doxygen to identify the file with a description that includes the copyright information.  To get around these kind of issues, the problematic section simply needs to use single `#` instead of a docstring.  Or start the file with a docstring describing the file prior to the copyright information.

#### Optionally install and configure doxypypy

Install doxypypy with pip (assuming all dependencies have already been installed).

```
sudo -H pip install doxypypy
```
Configure the Doxyfile.cfg by setting the *FILTER_PATTERNS* option.

```
FILTER_PATTERNS        = *.py=./py_filter
```
Create a py_filter script with the following contents in the directory containing the Doxyfile.cfg file.

```
#!/bin/bash
doxypypy -a -c $1
```

If doxypypy is to be utilized from a Jenkins fakeroot environment, you will likely need to use "easy_install" instead of "pip".

#### Manual testing of doxypypy

Try using the `doxypypy -a -c FILE.py` command to view the the results of FILE.py after doxypypy converts the comment blocks to a standard Doxygen style.

---
## Installing Doxygen from Scratch

```
apt-get install doxygen graphviz
doxygen -g Doxyfile.cfg
```

Modify the Doxyfile.cfg

* Change option PROJECT\_NAME to a meaningful name (i.e. Syndicate)
* Enable GENERATE\_MAN
* Disable GENERATE\_LATEX
* Enable EXTRACT\_STATIC to enable documentation of static functions
* Enable RECURSIVE to recursively scan through subdirectories
* Add README.md to EXCLUDE\_PATTERNS so the README files aren't included in the related pages
* Modify USE\_MDFILE\_AS\_MAINPAGE to point to the main page
* Change INPUT to the source directory (e.g. INPUT = syndicate-core syndicate-ug-tools ...)

Take note that the input paths are directories.  This means the syndicate repos will need to be downloaded prior to running doxygen against them.

There are two approaches for configuring Doxygen for our environment.

1) Run doxygen from each downloaded repo as seperate projects and create "tagfiles" (via GENERAGE_TAGFILE option).  Then run a final pass of doxygen that imports each tagfile and produces a final project.  This might be useful if we didn't want to have Doxygen scan through all of the repos every time the documentation is generated, since using this method tells Doxygen to only scan for the tagfiles.

2) Run doxygen once and provide all of the source directories as input.  This is more simple and works well for our environment since the documentation for each repo will be recreated each time the Jenkins detects a change in any of the repos. 

## Running Doxygen / Generating Documentation

If running from the syndicate-docs repo via the Makefile, simply run ... <br>

`make all`

Otherwise run ... <br>

`doxygen Doxyfile.cfg`

Since the output directory was not specified, this command produces Doxygen output (a html and man directory) in the directory it is run from.
