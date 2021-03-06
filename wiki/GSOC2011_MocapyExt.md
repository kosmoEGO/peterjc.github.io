---
title: GSOC2011 MocapyExt
permalink: wiki/GSOC2011_MocapyExt
layout: wiki
---

BioPython is a very popular library in Bioinformatics and Computational
Biology. Mocapy++ is a machine learning toolkit for parameter learning
and inference in dynamic Bayesian networks
(DBNs)<cite>PaluszewskiHamelryck2010</cite>, which encode probabilistic
relationships among random variables in a domain. Mocapy++ is freely
available under the GNU General Public Licence (GPL) from
[SourceForge](http://sourceforge.net/projects/mocapy). The library
supports a wide spectrum of DBN architectures and probability
distributions, including distributions from directional statistics.
Notably, Kent distribution on the sphere and the bivariate von Mises
distribution on the torus, which have proven to be useful in formulating
probabilistic models of protein and RNA structure.

Such a highly useful and powerful library, which has been used in such
projects as TorusDBN<cite>BMTFKH2008</cite>,
Basilisk<cite>HBPFJH2010</cite>,
FB5HMM<cite>HKK2006</cite><cite>PaluszewskiWinter2008</cite> with great
success, is the result of the long-term effort. The original Mocapy
implementation dates back to 2004, and since then the library has been
rewritten in C++. However, C++ is a statically typed and compiled
programming language, which does not facilitate rapid prototyping. As a
result, currently Mocapy++ has no provisions for dynamic loading of
custom node types, and a mechanism to plug-in new node types that would
not require to modify and recompile the library is of interest. Such a
plug-in interface would assist rapid prototyping by allowing to quickly
implement and test new probability distributions, which, in turn, could
substantially reduce development time and effort; the user would be
empowered to extend Mocapy++ without modifications and subsequent
recompilations. Recognizing this need, the project (herein referred as
MocapyEXT), with the aim to improve the current Mocapy++ node type
extension mechanism, has been proposed by T. Hamelryck.

Project objectives
------------------

The MocapyEXT project is largely an engineering effort to bring a
transparent Python plug-in interface to Mocapy++, where built-in and
dynamically loaded node types could be used in a uniform manner. Also,
externally implemented and dynamically loaded nodes could be modified by
a user and these changes will not necessitate the recompilation of the
client program, nor the accompanying Mocapy++ library. This will
facilitate rapid prototyping, ease the adaptation of currently existing
code, and improve the software interoperability whilst introducing
minimal changes to the existing Mocapy++ interface, thus facilitating a
smooth acceptance of the changes introduced by MocapyEXT.

Author & Mentors
----------------

Justinas V. Daugmaudis (vygis.d@gmail.com)

**Mentors**

Thomas Hamelryck

Eric Talevich

Work Plan
---------

'''Gain understanding of S-EM and directional statistics '''

-   Review the course material in SCI-B2-1011-Structural bioinformatics;

<!-- -->

-   Study the theory for Markov chain Monte Carlo methods and dynamic
    Bayesian networks;

<!-- -->

-   Supplement the base knowledge by reading relevant papers.

'''Study of Mocapy++ use cases '''

-   Study the Mocapy++ implementation of Stochastic Expectation
    Maximization (S-EM) and parameter learning of Bayesian networks;

<!-- -->

-   Review examples distributed with Mocapy++ library.

'''Study of Mocapy++ internals and code '''

-   Distill the relevant concepts for nodes and densities. Currently,
    there are no such concepts defined within the Mocapy++ framework;

<!-- -->

-   Compare with other examples that implement
    probability distributions.

'''Design of Mocapy++ plug-in interface '''

-   Plugin module has to implement the model of
    ESSConcept/DensitiesConcept;

<!-- -->

-   All the models have to be uniformly accessible through the
    plug-in system.

'''Implement Mocapy++ plug-in module '''

-   Implement test cases in C++ using the new plugin interface;

<!-- -->

-   Implement test cases in C++. Verify that Python modules can be used
    transparently in in Mocapy++.

'''Experiment with the modular Mocapy++ architecture '''

-   Create sample applications that showcase the advantages of
    modularity that is provided by the plug-in system in Mocapy++;

<!-- -->

-   Measure performance.

Schedule
--------

'''Week 1-2 \[May 23th -- June 5th\] '''

Design of interface strategy: it is hard to overstate the importance of
the plugin API feeling "natural" to a Mocapy++ user. So much of the time
will be devoted to the design of the interface.

'''Week 3-5 \[June 6th -- June 19th\] '''

Implement the plugin module.

'''Week 6-7 \[June 20th -- June 30th\] '''

Implement some examples that would showcase Mocapy++ plugin system in
action; for example, how to use externally implemented logistic
distribution.

'''Week 7-8 (Midterm) \[July 1st -- July 10th\] '''

-   Thorough testing and consolidating the features. Writing the
    documentation for each feature.

<!-- -->

-   Mid-term Evaluations. Discuss with the mentor the state of the
    project and adjust the schedule to comply with project's needs.

'''Week 9-10 \[July 11th -- July 24th\] '''

Example applications. Should focus on the type reflection capabilities
of the plugin module.

'''Week 11-12 \[July 25th -- August 7th\] '''

Update the documentation to reflect the new functions. Also document the
examples, do any scrub work of code, etc. Planned 'pencils down' date.

'''Week 13-14 \[August 8th -- August 21st\] '''

Introduce the bindings to the wider audiences, gather the opinions and
the reviews in community.

'''Week 15 \[August 22nd\] '''

The end of the project.

Source Code
-----------

Hosted at [the gSoC11 Mocapy
branch](http://mocapy.svn.sourceforge.net/viewvc/mocapy/branches/gSoC11/)

Progress
--------

### Definitions

For the uninitiated to the Mocapy++ terminology, it is necessary to
understand what ESS computer and Densities are, as these terms shall be
used throughout this article.

-   An ESS Computer is an archetype of the ESS class that contains the
    Expected Sufficient Statistics (ESS) for a given node. It implements
    how a sample (from the Gibbs sampler) should be added and stored,
    such that estimation of the distributions parameters can be done. An
    ESS computer class must be implemented for each node type. Also, it
    must satisfy the *ESSConcept* requirements.

<!-- -->

-   Densities is an archetype of Densities class that implements the
    estimation and sampling procedures of a given distribution.
    Densities must satisfy the *DensitiesConcept* requirements.

#### ESSConcept

In the following table, *X* denotes an ESS computer class, and *u* is a
mutable value of *X*.

| expression                 | return type | pre/post-condition                          |
|----------------------------|-------------|---------------------------------------------|
| u.construct(parent\_sizes) | void        | the appropriate shape of the ESS is defined |
| u.estimate(ess)            | void        | adds a sample point to the ESS              |

The class mocapy::BippoESS is an example model for an ESS computer.

#### DensitiesConcept

In the following table, *X* denotes a Densities computer class, *v* is a
const value of *X*, and *u* is a mutable value of *X*. Note that ptv
stand for "parent and this node’s values": the values of the parents
nodes and the value of the node to which the method belongs.

| expression                 | return type                      | pre/post-condition                                                                                                                                                     |
|----------------------------|----------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| u.construct(parent\_sizes) | void                             | parameters (mean, covariance, CPD, etc.) are initialized                                                                                                               |
| u.estimate(ess)            | void                             | the parameters of the node based on the given ESS are estimated                                                                                                        |
| u.sample(ptv)              | std::vector<double>              | returns a sample based on indicated parent values. Note, that subsequent calls to the sample member function may return different values, so this operation is mutable |
| v.get\_lik(ptv, log)       | double                           | returns likelihood, P(child|parent)                                                                                                                                    |
| v.get\_parameters()        | std::vector<MDArray<double> &gt; | returns the distributions parameters                                                                                                                                   |
| v.get\_node\_size()        | unsigned int                     | returns the node size                                                                                                                                                  |
| v.get\_output\_size()      | unsigned int                     | returns the output size of the node                                                                                                                                    |

The class mocapy::BippoDensities is an example model for a Densities
computer.

### MocapyEXT implementation

#### Embedding the Python interpreter

For a client program to be able to execute Python code, it is necessary
to initialize the scripting environment. It is done by invoking the
Py\_Initialize() function. The interpreter is released by invoking the
Py\_Finalize() function.

It is important to note, however, that any statement between
Py\_Initialize() and Py\_Finalize() calls could throw an exception. If
an exception is thrown, it must be either handled in a try/catch block,
or the program must be terminated. Having this in mind, an previous
example program could be made more robust by enveloping the statements
between Py\_Initialize() and Py\_Finalize() in a try/catch block.
Py\_Finalize() safety could not be overstated. Currently Boost.Python
has several global (or function-static) objects whose existence keeps
reference counts from dropping to zero until the Boost.Python shared
object is unloaded. This can cause a crash because when the reference
counts do go to zero, there is no interpreter. This poses a question if
such a method of initializing the Python interpreter could be considered
to be "easy to use", "safe" or even "non-intrusive", which are the major
MocapyEXT design tenets.

The solution to this issue that is easy to use, safe and non-intrusive
is surprisingly elegant and showcases the RAII (Resource Acquisition Is
Initialization) idiom.

#### The lifetime of the Python interpreter

The Python interpreter is initialized before the entry to the main
function, and is released after the exit from the main function. No
additional end-user effort is required, except to include the headers
that define the necessary Python plug-in wrappers.

#### Compile/link dependencies

The Python interpreter shall be linked to the client program (not to the
Mocapy++ library). The end-user of the MocapyEXT shall be responsible
for additional include and/or library paths to the Python headers and
libraries that are necessary to compile and link the client program
successfully.

MocapyEXT also instantiates its static data members in such a manner
that they are instantiated only once and in the library's compilation
unit. In the C++ standard it is explicitly stated that

"... in particular, the initialization (and any associated side-effects)
of a static data member does not occur unless the static data member is
itself used in a way that requires the definition of the static data
member to exist."\[cpp\_std2003, temp.inst\]

The user shall not need to manage the instantiated static data members.

#### Thread safety guarantees

The MocapyEXT plug-ins have the following thread-safety guarantees:

-   it is safe to call plug-in member functions from multiple threads;

<!-- -->

-   it is safe to perform concurrent read-only accesses of the passed
    parameter containers;

<!-- -->

-   it is safe to perform concurrent read/write accesses, if there is
    only one read or write access to the passed factual parameters at
    a time.

Concurrent mutable access of the passed parameter containers requires
synchronization, for example via reader-writer lock. Mutable access
includes altering values within the container, invoking member functions
that invalidate iterators, moving the container via the move
constructor.

#### The module search path

When a module foo that contains ESS or Densities definitions is being
imported, the embedded Python interpreter searches for a file named
foo.py in the list of directories specified by the environment variable
PYTHONPATH and the current path.

Before proceeding to import a module, sys.path list is explicitly
appended with the current path. A user that relies on scripts being in
some other directory than a client program, should list the paths in the
PYTHONPATH environment variable.

Note that because the interpreter also searches in an
installation-dependent default path, it is important that the file
containing ESS or Densities definitions not have the same name as a
standard module.

#### Plugin registration

The minimal interface import example shows the usage of three plug-in
classes: densities\_plugin, ess\_plugin and aggregate\_plugin. The
intended usage pattern for densities\_plugin and ess\_plugin are cases
when types ESS and Densities are implemented in separate files --
one-class-per-file. Each class is loaded in their own interpreter
context. The purpose of aggregate\_plugin is to simplify the management
of the the loaded types and to optimize the resources allocated for the
embedded Python interpreter as aggregate\_plugin creates only one
instance of the embedded interpreter for both types ESS and Densities.

<cpp>

`#include <mocapy/plugin/ess_plugin.hpp>`
`#include <mocapy/plugin/densities_plugin.hpp>`
`#include <mocapy/plugin/aggregate_plugin.hpp>`

`int main()`
`{`
`  using namespace mocapy::ext;`
`  densities_plugin dens_pl("plugin_tests", "DensitiesPython");`
`  ess_plugin ess_pl("plugin_tests", "ESSPython");`
`  aggregate_plugin aggr_pl("plugin_tests", "ESSPython", "DensitiesPython");`
`  return 0;`
`}`

</cpp>

Basically, a user provides a name of a python library, a name of class
and a list of factual arguments necessary for the construction of the
model of the class. The MocapyExt library in turn then returns an
instance of the new class.

<cpp>

`densities_plugin p("test_module", "DensitiesClassName");`
`densities_adapter n = p.densities(arg1, arg2, ..., argN);`

</cpp>

The given *argument list* (up to N = 6 arguments are supported) would
parametrize the initialization of Densities object. Parameters are
forwarded to the Python API and converted using the type conversion
facilities provided by the Boost.Python library. The mechanism is
general and the user can convert any arbitrary type T to its equivalent
in Python.

The parameters are forwarded via const-references. This method accepts
and forwards arbitrary typed arguments, at the cost of always treating
the argument as const. This solution is typically used for constructor
arguments. An esoteric problem with this approach is that it is not
possible to form a const reference to a function type, but this
deficiency, which is being addressed in Core Issue
[295](http://std.dkuug.dk/jtc1/sc22/wg21/docs/cwg_active.html#295),
actually works in our favor, because this prevents ill-intended attempts
to pass a function to the Python interpreter.

Any further invocation of ess and dens member functions automatically
calls a corresponding class method in Python.

#### The lifetime of a class instance

The lifetime of ESS computer and Densities objects (respectively, ess
and dens) may exceed the lifetime of their respective factory plugin(s).
Objects ess and dens preserve reference counted pointers to the Python
interpreter, thus a valid Python interpreter instance exists until the
destruction of objects ess and dens.

#### Creation of a node

This is a simple example that showcases two different methods to
initialize a plugin node. In this particular case, the module
plugin\_tests that has been used in the program implements a dummy
discrete node of fixed length 1, and always returns \[0,\] as sampled
value.

<cpp>

`#include <mocapy/plugin/ess_plugin.hpp>`
`#include <mocapy/plugin/densities_plugin.hpp>`
`#include <mocapy/plugin/aggregate_plugin.hpp>`
`#include <mocapy/plugin/plugin_node.hpp>`

`int main()`
`{`
`  using namespace mocapy::ext;`
`  // Node initialization from two separately loaded modules`
`  {`
`    ess_plugin ess("plugin_tests", "ESSPython");`
`    densities_plugin dens("plugin_tests", "DensitiesPython");`
`    plugin_node_type node(ess.ess(arg1, arg2, ..., argN), dens.densities(arg1, arg2, ..., argN));`
`  }`
`  // Node initialization from a single loaded module`
`  {`
`    aggregate_plugin pl("plugin_tests", "ESSPython", "DensitiesPython");`
`    plugin_node_type node(pl.ess(arg1, arg2, ..., argN), pl.densities(arg1, arg2, ..., argN));`
`  }`
`  return 0;`
`}`

</cpp>

plugin\_node\_type is a typedef of the ChildNode class template. Here we
also note that the lifetime of a node is independent from the lifetime
of plug-in factories.

#### Node output

A plugin node is *Streamable*, i.e., it can be output to a std::ostream
via the operator

<cpp>std::ostream& operator&lt;&lt;(std::ostream&, const
plugin\_node\_type&);</cpp>

The content of the output is the same as *repr(Z)*, where *Z* is either
an ESS computer or Densities object.

#### Node serialization

The MocapyEXT preserves the old Mocapy++ ChildNode class template
serialization behaviour, which means that for ESS computers that do not
need to be serialized (this includes kentess, gaussianess, poissoness,
and others) no serialization will happen. However, the MocapyEXT
necessarily has to serialize the Python plugin that implements an ESS
computer in order to preserve the module and the name of the class, so
that the ESS computer could be later deserialized.

It also must be noted that after deserialization the ESS computer and
Densities will not share the same Python interpreter instance, even if
they are implemented in the same module.

### Measuring performance

It is interesting to measure how much plugin interface costs compared to
the implementation of ESS and Densities computers themselves.

We measure the relative performance of tests named N, S, and A. All
tests invoke the member functions of the ESS and Densities computers,
albeit no particular computation is carried out.

N test stands for N(ative), which means that pure C++ implementation of
ESS and Densities computers were used in the test.

S test stands for S(eparate); Python classes are loaded in separate
Python interpreter instances.

A test stands for A(ggregate); ESS and Densities computers are loaded in
the same Python interpreter instance.

`            Conf. int.     4.53e+04     5.03e+04     5.53e+04`

Speedup percentages based on Wilcoxon matched pair confidence intervals.

`  Func. A vs   Func. B      Minimum       Median      Maximum`
`                         (% faster)   (% faster)   (% faster)`
`     N    vs   S                717          719          724`
`     N    vs   A                718          725          730`
`     A    vs   S             -0.673       -0.251       0.0774`

Test results show that using MocapyEXT plugin interface incurs some
performance penalty. It must be noted, however, that tests also
performed repetitive construction of ESS and Densities instances.

`            Conf. int.     1.94e+04     2.15e+04     2.37e+04`

Speedup percentages based on Wilcoxon matched pair confidence intervals.

`  Func. A vs   Func. B      Minimum       Median      Maximum`
`                         (% faster)   (% faster)   (% faster)`
`     N    vs   S                240          242          243`
`     N    vs   A                239          243          245`
`     A    vs   S              -1.24        -0.51        -0.25`

Repeated tests without the construction of ESS and Densities objects
show that invocation of a method through the MocapyEXT interface is only
~2,5 times slower than to invoke a member function of natively
implemented ESS and Densities objects. It is obvious that in a
real-world scenario node construction, sampling, likelihood computation,
etc. would be more likely to constitute the better part of the running
time as compared to the invocations of methods themselves. Basically,
these tests show that member function invocation takes much less time
than even such a 'lightweight' operation as node construction. In
principle it is now possible to write quite generic algorithms. Consider
this example:

<cpp>

` template`<typename Densities>
` inline void do_something_with_densities(Densities dens) // Generic function `
` {`
`   std::vector`<unsigned int>` ps(1, 2);`
`   dens.construct(ps);`
`   /* do something here */`
` }`

` // Bippo Densities though the Python interface`
` densities_plugin dens("mocapy.bippo", "BippoDensities");`
` do_something_with_densities( dens.densities(lambdas, thetas, ns) );`

` // Direct use of Bippo Densities`
` mocapy::BippoDensities bd(lambdas, thetas, ns);`
` do_something_with_densities(bd);`

</cpp>

We can be reasonably sure that performance penalty of using MocapyEXT,
while not negligible, will not outweight the added value of genericity.
And in the pure C++ scenario it does not incur any performance cost.

### Roadmap

The MocapyEXT is an ongoing project. In the current state it is usable,
however, it is important to stress that the described features are by no
means complete and may work differently in the future. Numerous changes
have been made in Mocapy++ to support the MocapyEXT infrastructure. Most
notably, the Mocapy++ library has been made partially const-correct in
the development branch. The importance of const-correctness cannot be
overstated as it affects the parameter passing protocol from and to the
Python code. The changes, however, have been done transparently. If an
end-user did not rely on the side effects of the now immutable
operations, she should be able to compile and run the code with minor
modifications. Undoubtedly, many other changes await. For example, in
some contexts it would be useful to have the ability to clone the
Densities by passing their parameters to the constructor:

<cpp>

` mocapy::BippoDensities bd(lambdas, thetas, ns);`
` mocapy::BippoDensities clone( bd.get_parameters() );`

` // The objects must have the same state!`
` assert( bd == clone );`

</cpp>

It is also my belief that the current parameter initialization scheme is
ill conceived because the pointer to a random number generator is stored
in Densities. It would be much better to pass the random number
generator as a parameter. This would singlehandedly solve problems with
[object
ownership](http://biopython.org/wiki/GSOC2011_Mocapy#Testing_and_Improving_Mocapy_Bindings)
in Mocapy++ Python bindings, fix some left-over const-correcness issues
and so on. However, this would introduce a breaking change, and a lot of
projects depend on Mocapy++.

During the project one of the interesting technical exercises has been
to convert the values of the MDArray class template to Python's ndarray.
The conversion routines have been written and all of them fully work as
expected, however, the MDArray class template has its problems. It is
one of the classic examples of failed [separation of
concerns](http://en.wikipedia.org/wiki/Separation_of_concerns). Not only
it serves as a multi-dimensional array, but it is used in a manner that
one would use a Swiss army knife: the MDArray also serves as a matrix,
as a vector (in the mathematical sense), it has curiously named member
functions like void keep\_eye() (I still do not know what it does), it
could be rotated, axis permuted, and whatever else one can think of --
all of which is implemented in member functions. MDArray interface is
enormous, and this is clearly wrong. Still, touching MDArray is a
delicate matter that ought to be addressed with utmost care; most likely
by introducing free functions to do the job, and slowly phasing out the
member functions.

References
----------

<biblio>

1.  PaluszewskiHamelryck2010 M. Paluszewski and T. Hamelryck. Mocapy++
    -- a toolkit for inference and

learning in dynamic bayesian networks. BMC bioinformatics, 11(1):126,
2010.

1.  BMTFKH2008 W. Boomsma, K.V. Mardia, C.C. Taylor, J.
    Ferkinghoff-Borg, A. Krogh, and T. Hamelryck.

A generative, probabilistic model of local protein structure.
Proceedings of the National Academy of Sciences, 105(26):8932–8937,
2008.

1.  HBPFJH2010 Tim Harder, Wouter Boomsma, Martin Paluszewski, Jes
    Frellsen, Kristoffer

Johansson, and Thomas Hamelryck. Beyond rotamers: a generative,
probabilistic model of side chains in proteins. BMC Bioinformatics,
11(1):306, 2010.

1.  HKK2006 T. Hamelryck, J.T. Kent, and A. Krogh. Sampling realistic
    protein conformations

using local structural bias. PLoS computational biology, 2(9):e131,2006.

1.  PaluszewskiWinter2008 M. Paluszewski and P. Winter. Protein decoy
    generation using branch and

bound with efficient bounding. Algorithms in Bioinformatics, pages
382–393, 2008. </biblio>
