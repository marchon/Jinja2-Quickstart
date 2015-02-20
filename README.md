# Jinja2-Quickstart
A Quickstart Guide to Using the Jinja2 Template Engine by  Bryan Hill (kagerato)  © Copyright 2010-2012

#A Quickstart Guide to Using the Jinja2 Template Engine

#[This original article is © Copyright 2010-2012 Bryan Hill (kagerato)  
##https://www.kagerato.net email him at kagerato[at]gmail.com.

view the original article here (http://kagerato.net/articles/software/libraries/jinja-quickstart.html)

Why I a copy of it here
------
I had such a hard time reading and absorbing it due to his color scheme.

I have invited him to this project.
------


#A Quickstart Guide to Using the Jinja2 Template Engine
##What is Jinja 2 ?

Jinja2 is the second major version of a Python library used to generate documents based on one or more predefined templates. One common use case is as part of the backend for web sites which need to create many similar pages. However, it can be used to produce any kind of text document, not merely those related to the web.

This library was written by Armin Ronacher, with version 2.0 being first released in July of 2008. It is stable and largely feature complete. The newest version is 2.6, which was released in July of 2011.

As to the name, "jinja" is the Japanese word for a Shinto shrine or temple. Temple, template... a clever pun.

Advantages of Jinja
Due to the fact that there are many templating engines for Python, one may legitimately wonder what makes Jinja stand apart. Primarily, the strengths of Jinja are found in its simplicity. It has a pleasant, familiar syntax that closely mimics what many Python developers would expect. The features are general and do not try to arbitrarily constrain the user into following a particular development philosophy. Good security practices are encouraged by default. Finally, the templates compile at runtime into Python code, improving performance by eliminating both the need to reparse and any unnecessary intermediate formats.

Jinja has been used by prominent organizations, including Mozilla and SourceForge. Further, it has been downloaded over 50,000 times from PyPi (a significant Python code hub). This suggests it has reasonably high popularity among independent developers.

##Installation
There are several ways to install Jinja. The recommended method is to use easy_install or pip. If your Python installation already has one of these modules available, you can simply run easy_install Jinja2 or pip install Jinja2 and the rest will be managed for you.

##API Overview
There are two key objects in the Jinja API: Environment and Template. Environment objects are used to initialize, store, and configure variables which are significant to template rendering. Template objects, on the other hand, represent a particular template file and can be used to generate one or more outputs from it.

Beyond these key objects, there is also a secondary set used for reading the template files. These objects are classified as Loaders. The loaders of significance for typical use are FileSystemLoader, PackageLoader, and DictLoader. The three of these read template data from the file system, a Python package, or a Python dictionary, respectively.

Ordinarily, the process one will use to transform a template and its inputs into a rendered output file has four steps. First, select and construct an appropriate loader object. Second, create an environment object while specifying that new loader and any other desired options. Third, use the environment's get_template method to read the template file using the loader, and store the resulting template object. Fourth and finally, process the template by passing any inputs into the render method of the template object.

##Leading by Example
###Listing 1a: sample Python code

  ```python

  # Load the jinja library's namespace into the current module.
  import jinja2

  # In this case, we will load templates off the filesystem.
  # This means we must construct a FileSystemLoader object.
  #
  # The search path can be used to make finding templates by
  #   relative paths much easier.  In this case, we are using
  #   absolute paths and thus set it to the filesystem root.
  templateLoader = jinja2.FileSystemLoader( searchpath="/" )

  # An environment provides the data necessary to read and
  #   parse our templates.  We pass in the loader object here.
  templateEnv = jinja2.Environment( loader=templateLoader )

  # This constant string specifies the template file we will use.
  TEMPLATE_FILE = "/home/user/site/example1.jinja"

  # Read the template file using the environment object.
  # This also constructs our Template object.
  template = templateEnv.get_template( TEMPLATE_FILE )

  # Specify any input variables to the template as a dictionary.
  templateVars = { "title" : "Test Example",
                   "description" : "A simple inquiry of function." }

  # Finally, process the template to produce our final text.
  outputText = template.render( templateVars )
```
###  Listing 1b: sample Jinja template

```html
  <!doctype html>
  <html lang="en">
  <head>
    <meta charset="UTF-8" />

    <title>{{ title }}</title>
    <meta name="description" content="{{ description }}" />
  </head>

  <body>

  <div id="content">
    <p>Why, hello there!</p>
  </div>

  </body>
  </html>
```
##Template Input Variables

Most of what appears above will be familiar to those versed in Python and HTML. The one aspect which is different, and begins to touch on Jinja's features, is the template input. It is possible to pass arbitrary strings of text into predefined placeholders in the actual template file itself. Simple variables like this are marked in the template using the {{ variable_name }} syntax.

As one would imagine, the Python code can determine the contents of these variables at runtime. Further, it can perform multiple separate renderings of the same template with different input variable values. There is no arbitrary limit to the number of inputs which may be specified this way, either. These facts allow for a great deal of flexibility.

In some cases, this simple input substitution will be enough for the user's needs. Especially for purists who do not want to mix any kind of logic into the template file itself, it makes sense to do all manner of processing in the Python code and then output it to the template through variables like these.

However, there are those who do not agree with that philosophy. They wish to insert presentation logic into the template, and regard processing HTML or other disparate code in the Python as unwise. Jinja is able to accommodate them.

##Template Logic

Let us look at how to incorporate some fundamental logic into a template in order to keep the Python code simple.

##Listing 2a: Python rendering code

```python
import jinja2

templateLoader = jinja2.FileSystemLoader( searchpath="/" )
templateEnv = jinja2.Environment( loader=templateLoader )

TEMPLATE_FILE = "/home/user/site/example2.jinja"
template = templateEnv.get_template( TEMPLATE_FILE )

# Here we add a new input variable containing a list.
# Its contents will be expanded in the HTML as a unordered list.
FAVORITES = [ "chocolates", "lunar eclipses", "rabbits" ]

templateVars = { "title" : "Test Example",
                 "description" : "A simple inquiry of function.",
                 "favorites" : FAVORITES
               }

outputText = template.render( templateVars )
```
Listing 2b: Jinja template with a loop

```html
<!doctype html>
<html lang="en">
<head>
  <meta charset="UTF-8" />

  <title>{{ title }}</title>
  <meta name="description" content="{{ description }}" />
</head>

<body>

<div id="content">
  <p>Greetings visitor!  These are a list of my favorite things:</p>

  <ul>
  {% for item in favorites %}
    <li>{{ item }}</li>
  {% endfor %}
  </ul>
</div>

</body>
</html>
````

#### Take note of both the for loop added to the template, and the additional list variable in the Python code. In particular, pay attention to the fact that logic expressions use a {% ... %} syntax. Using the variable expansion syntax would be a template bug. More implications are discussed below.

Jinja supports a subset of control structures that are very similar to Python. The critical two are if and for. Their syntax is similar to Python, only differing in that no colon is required at the end of the statement and that termination of the block is done using an endif or endfor instead of by whitespace. Most user needs for presentational logic can be met with just these two elements.

In the above example, a list of strings is iterated over in a very similar manner to how it would be done in Python. A temporary variable, item, will store the current value of an element of the list for each iteration of the loop. In this case, we merely print the item value inside a list item tag.

Of course, one could do the same effective work in the Python code instead. This would involve a series of selective string concatenations with HTML inline. Then the preprocessed output would be written via a template variable. This works, but has a significant issue with structural clarity. It may also be difficult to maintain an exact formatting of whitespace, especially in a complicated template.

##Further Exploration
This article merely scratches the surface of Jinja's features. Several more powerful tools also exist, including filters, macros, blocks, and template inheritance. However, these have been omitted as their high complexity may be excessive for new users. Readers are encouraged to peruse the Jinja official documentation and code samples as they become more comfortable with the library.


© Copyright 2010-2012 Bryan Hill (kagerato).
