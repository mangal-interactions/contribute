# Contribute data to mangal.io

**Thank you for contributing your data**. The aim of this repository is to make
the submission process as easy as possible. In fact, we will do the actual
injection in the database *for you*. Why? There are a few reasons:

- we know how to do it!
- we know how to troubleshoot it!
- you have other things to do!

## How can I contribute?

There are three steps to submitting a dataset (and one of them is only taking a
few seconds!)

### Signing up as a mangal user

This is not something you actually have to do. If you have an [ORCID] ID, all
you need to do is click on <https://mangal.io/auth>, login with ORCID, and you
will have an account. We will ask for your ORCID during the submission process
if you want it to be linked to the data.

That's it. Welcome to the user list.

### Preparing your data according to our templates

This step is probably the longest, and if we're being honnest, it's not 100%
mandatory. As of Nov. 2021, we have added about 130k interactions by writing
bespoke scripts for each dataset. It's just that it takes longer, and often
involves a number of back and forth emails to clarify points of data structure.

To make sure that the data are published as rapidly and efficiently as possible,
it is a good idea to attach the templates to your data submission issue (see the
next section!). The links to the templates can be found
[here][templates].

### Opening an issue describing the dataset

The last step is to [open an issue][issue]

## The templates
## Additional questions

**What is the license of the data on mangal.io**? CC-0 by default, or the
license of the dataset that was previously uploaded to *e.g.* Dryad, figshare,
etc.

**How do I know the data will not be uploaded wrong**? The data submission
script is using a number of sanity checks to make sure the data are injected
exactly as provided. Nothing is done without human supervision.

**Why not use a pull request to publish data**? Having spent a lot of time
around code, we trust it less than we trust human. Manual inspection of data
will most likely always be how we proceed.

**I found an error in the data**! That's not a question but fine; there is a
specific type of issue (called *Data Issue*) [you can open][issue] on this
repository, and we will examine and fix the issue.

<!-- Below are the links used in the document -->

[ORCID]: https://orcid.org/
[issue]: https://github.com/mangal-interactions/contribute/issues/new/choose
[templates]: https://github.com/mangal-interactions/contribute#the-templates