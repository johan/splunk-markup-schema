# splunk-markup-schema

Since splunk 8.2 SimpleXML RelaxNG schemas neither map to their own docs nor implementation, this brings the schema closer to them both.

To verify a file to the schema with `xmllint`, try

```sh
xmllint --quiet --noout --relaxng schema/simplexml-8.2.6.1.rng dashboard.xml
```

(If you want console output for the happy case, drop `--quiet`.)

If you get errors, you may want to squint; at least the one shipping native in macOS 13.0 Ventura isn't great:

```sh
> xmllint --version
xmllint: using libxml version 20913
   compiled with: Threads Tree Output Push Reader Patterns Writer SAXv1 FTP HTTP DTDValid HTML Legacy C14N Catalog XPath XPointer XInclude ICU ISO8859X Unicode Regexps Automata Schemas Schematron Modules Debug Zlib
```

In an example error like this:

```
Relax-NG validity error : Extra element panel in interleave
dashboard.xml:371: element panel: Relax-NG validity error : Element row failed to validate content
Relax-NG validity error : Extra element row in interleave
dashboard.xml:370: element row: Relax-NG validity error : Element form failed to validate content
dashboard.xml fails to validate
```

…that line number will probably point to one or more ancestors of where the problem is, and the error message might talk about that same ancestor instead of the culprit.

Instead, to find and fix a flagged problem, plonk a

```xml
<?xml-model href="schema/simplexml-8.2.6.1.rng" type="application/xml"?>
```

line at the top of your xml file (adjusting the href to match your paths),
load it up in something like [VSCode](https://code.visualstudio.com/) with
[RedHat's XML extension](https://marketplace.visualstudio.com/items?itemName=redhat.vscode-xml),
and it'll draw squgglies around the real culprits,
offer tooltips stating what's wrong, and so on.

Yes, this isn't based on the very latest splunk, but the xml format doesn't seem to change much.
To bring it up to 9.0.3 specs, run this:

```sh
patch -p1 < diff-8.2.6.1-to-9.0.3.patch schema/simplexml-8.2.6.1.rng
```
