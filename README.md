# hcana_container_doc

Sphinx is a python documentation generating tool (that can be used for other languages) that uses a markup language
called "reStructuredText" to convert files into HTML webpages and other format types (like PDF and LaTex).

To make html locally you need to do the following.

``` shell
git clone git@github.com:panta-123/hcana_container_doc.git
```
cd into the cloned folder.

Setup the virtual environment and activate it:
```shell
python3 -m venv .env
source .env/bin/activate
```
Install using (only should be done once.)
```shell
pip install sphinx sphinx-rtd-theme sphinx-copybutton
```
 Now build the doc
```shell
make html
```
