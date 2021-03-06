SRCDIR = ../src

clean:
	rm -fr me.nlohmann.json.docset html


##########################################################################
# example files
##########################################################################

# where are the example cpp files
EXAMPLES = $(wildcard examples/*.cpp)

# create output from a stand-alone example file
%.output: %.cpp
	make $(<:.cpp=) CPPFLAGS="-I $(SRCDIR)" CXXFLAGS="-std=c++11"
	./$(<:.cpp=) > $@
	rm $(<:.cpp=)

# compare created output with current output of the example files
%.test: %.cpp
	make $(<:.cpp=) CPPFLAGS="-I $(SRCDIR)" CXXFLAGS="-std=c++11"
	./$(<:.cpp=) > $@
	diff $@ $(<:.cpp=.output)
	rm $(<:.cpp=) $@

# create links to try the code online
%.link: %.cpp
	rm -fr tmp
	mkdir tmp
	cp $(SRCDIR)/json.hpp tmp
	./send_to_wandbox.py tmp $< > $@.tmp
	/bin/echo -n "<a target=\"_blank\" href=\"`cat $@.tmp`\"><b>online</b></a>" > $@
	rm -fr tmp $@.tmp

# create output from all stand-alone example files
create_output: $(EXAMPLES:.cpp=.output)

create_links: $(EXAMPLES:.cpp=.link)

# check output of all stand-alone example files
check_output: $(EXAMPLES:.cpp=.test)


##########################################################################
# Doxygen HTML documentation
##########################################################################

# create Doxygen documentation
doxygen: create_output create_links
	doxygen
	gsed -i 's@&lt; ObjectType, ArrayType, StringType, BooleanType, NumberIntegerType, NumberFloatType, AllocatorType &gt;@@g' html/*.html
	gsed -i 's@&lt;&#160;ObjectType,&#160;ArrayType,&#160;StringType,&#160;BooleanType,&#160;NumberIntegerType,&#160;NumberFloatType,&#160;AllocatorType&#160;&gt;@@g' html/*.html

upload: doxygen check_output
	cd html ; ../git-update-ghpages nlohmann/json
	rm -fr html
	open http://nlohmann.github.io/json/


##########################################################################
# docset
##########################################################################

# create docset for Dash
docset: create_output
	cp Doxyfile Doxyfile_docset
	gsed -i 's/DISABLE_INDEX          = NO/DISABLE_INDEX          = YES/' Doxyfile_docset
	gsed -i 's/SEARCHENGINE           = YES/SEARCHENGINE           = NO/' Doxyfile_docset
	gsed -i 's@HTML_EXTRA_STYLESHEET  = css/mylayout.css@HTML_EXTRA_STYLESHEET  = css/mylayout_docset.css@' Doxyfile_docset
	rm -fr html *.docset
	doxygen Doxyfile_docset
	gsed -i 's@&lt; ObjectType, ArrayType, StringType, BooleanType, NumberIntegerType, NumberFloatType, AllocatorType &gt;@@g' html/*.html
	gsed -i 's@&lt;&#160;ObjectType,&#160;ArrayType,&#160;StringType,&#160;BooleanType,&#160;NumberIntegerType,&#160;NumberFloatType,&#160;AllocatorType&#160;&gt;@@g' html/*.html
	make -C html
	mv html/*.docset .
	gsed -i 's@<string>doxygen</string>@<string>json</string>@' me.nlohmann.json.docset/Contents/Info.plist
	rm -fr Doxyfile_docset html
