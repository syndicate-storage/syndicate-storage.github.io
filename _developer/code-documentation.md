---
layout: post
title: A Syndicate CI/CD Strategy
---

# Documenting Syndicate with Doxygen
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

Typically, developers choose to solely use these two styles for the sake of consistency.  Furthermore, comment blocks are often written in header files before the declaration it describes (with the same indentation level).

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

#### @note
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

Indicates that a comment block contains documentation for a source or header file with name <name>.

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

---
## Installing from Scratch

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