check: prepare val-schema html mount

# Validate and compile ODD to RelaxNG, validate samples, make HTML
all:	prepare val-schema xml-schemas val-samples html
xall:	prepare val-schema xml-schemas val-samples html

# Builds html and pushes it
deploy:	html
	git commit -m "Deploy GitHub pages" ../docs
	git push

# Put the docs directory somewhere where the HTML can be viewed
# This is a local thing!
mount:
	rsync -a ../docs/ tomaz@nl.ijs.si:/home/tomaz/www/tmp/ParlaMint/Pages

# ParlaMint TEI documentation and schema, as expanded by prepare target
ODD = tmp/ParlaMint.xml

# The infamous TEI P5 subset from the Guidelines, either local or from the Web
SUBSET = --localsource=p5subset.xml
#SUBSET = --localsource=https://tei-c.org/release/xml/tei/odd/p5subset.xml

# Location and directory name of the profile for conversion to HTML
PROFILE = --profiledir=$(shell pwd) --profile=profile

# Convert to HTML
html:	prepare val-schema
	Stylesheets/bin/teitohtml ${PROFILE} --odd ${SUBSET} ${OUTPUT} ${ODD} ../docs/index.html

# Compile schemas to RelaxNG, DTD and XSD; best use the RelaxNG one.
xml-schemas:	val-schema
	Stylesheets/bin/teitorng        ${SUBSET} ${ODD} ParlaMint.odd.rng
	Stylesheets/bin/teitornc        ${SUBSET} ${ODD} ParlaMint.odd.rnc
	#Stylesheets/bin/teitoschematron ${SUBSET} ${ODD} ParlaMint.odd.sch
	#Stylesheets/bin/teitodtd        ${SUBSET} ${ODD} ParlaMint.odd.dtd
	#Stylesheets/bin/teitoxsd        ${SUBSET} ${ODD} ParlaMint.odd.xsd

val:	val-schema val-samples
val-schema:
	$j tei_odds.rng tmp/ParlaMint.xml

# Validate samples
DATA = ../Samples
val-samples:
	./validate.pl samples ${DATA}

# Validate all the full set of components; this is a local thing!
val-master:
	./validate.pl master ../../V2/MasterV2.1

# Make one file from the ODD
prepare: generate-schemaSpecs
	rm -f tmp/*
	$s -xi -xsl:copy-odd.xsl ParlaMint.odd.xml > tmp/ParlaMint.xml

# This was done, and we don't want to re-run this script.
xxx-fix-pm:
	$s -xsl:add-pm.xsl src=ParlaMint-schemaSpecs.orig.odd.xml ParlaMint-schemaSpecs.editing.bad.odd.xml \
	> ParlaMint-schemaSpecs.editing.odd.xml
	diff -w ParlaMint-schemaSpecs.editing.bad.odd.xml ParlaMint-schemaSpecs.editing.odd.xml

# This was done, and we don't want to re-run this script.
xxx-prepare-p5subset:
	$s -xsl:add-desc.xsl ParlaMint-schemaSpecs.editing.original.odd.xml | \
	$s -xsl:add-source.xsl - > ParlaMint-schemaSpecs.editing.odd.xml

# generate schemaSpecs.odd file
generate-schemaSpecs:
	$s -xi -xsl:generate-odd.xsl tei_odd=p5subset.xml ParlaMint-schemaSpecs.editing.odd.xml > ParlaMint-schemaSpecs.odd.xml

# Initialise
setup:
	mkdir -p tmp
	git -C ../Scripts clone git@github.com:TEIC/Stylesheets.git

#Prerequisites
s = java -jar ../Scripts/bin/saxon.jar
j = java -jar ../Scripts/bin/jing.jar
