MAKEFLAGS += --warn-undefined-variables
SHELL := bash
.SHELLFLAGS := -eu -o pipefail -c
.DEFAULT_GOAL := all
.DELETE_ON_ERROR:
.SUFFIXES:

scripts_url := http://unicode.org/Public/UNIDATA/Scripts.txt
unicode_data_url := ftp://ftp.unicode.org/Public/UNIDATA/UnicodeData.txt

Scripts.txt:
	wget $(scripts_url)

UnicodeData.txt:
	wget $(unicode_data_url)

utf8_script.c: Scripts.txt utf8_script.c.erb
	./generate_script.rb $^ > $@

utf8-script: utf8_script.c
	gcc -O2 -o $@ $<

utf8_category.c: UnicodeData.txt utf8_category.c.erb
	./generate_category.rb $^ > $@

utf8-category: utf8_category.c
	gcc -O2 -o $@ $<


.PHONY: all
all: utf8-script utf8-category

output/utf8-script output/utf8-category:
	mkdir -p $@

output/utf8-script/%: test/utf8-script/input/% | output/utf8-script
	./utf8-script < $< > $@
	diff test/utf8-script/expected.output/$* $@

output/utf8-category/%: test/utf8-category/input/% | output/utf8-category
	./utf8-category < $< > $@
	diff test/utf8-category/expected.output/$* $@

utf8_script_tests := one two three
utf8_category_tests := one two three four five
utf8_script_test_files := $(patsubst %,output/utf8-script/%.txt,$(utf8_script_tests))
utf8_category_test_files := $(patsubst %,output/utf8-category/%.txt,$(utf8_category_tests))
test_files := $(utf8_script_test_files) $(utf8_category_test_files)

.PHONY: test
test: $(test_files)

rubocop:
	find . -name '*.rb' | xargs rubocop -D

clean:
	rm -rf output

clobber: clean
	rm utf8-script utf8-category utf8_script.c utf8_category.c
