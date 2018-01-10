# This makefile makes some assumptions (in order of seriousness):
# GNU find exists: We currently use -printf which is GNU find specific. -type (d, and f for directories and files), and -name including wildcards.
# rm exists and supports -rf which removes everything recursively, supporting wildcards.
# echo exists and parses \n as a new line.
# src/ and bin/ exist in the same directory as this makefile.

# Changes required for use on Windows:
# Install the programs described above (or provide replacements).
# Change CP_SEP to a semi-colon.

# Locations for source files, and destination files.
# The trailing slashes are significant, we assume they exist for the clean directive.
SRC_LOCATION = src/
BIN_LOCATION = bin/

# Tools used in this makefile. Requirements for functionality at the top.
FIND = find
RM = rm -rf
ECHO = echo

# -g: Generate debugging information
# -d directory: Generate class files at directory
# -Werror: Treat warnings as errors
COMPILER_FLAGS = -g -d $(BIN_LOCATION) -Werror

# Flags that may not be supported. Comment out the line if you don't want to use them.
OPTIONAL_FLAGS = -Xlint

COMPILER = javac
EXECUTION = java

# The class path demiliter
CP_SEP = :

# The class path is: ".X" where X is the result of find.
# find finds all of the directories below bin and prints them as :dir:dir:dir... or ;dir;dir;dir...
# The result is a classpath that includes the current directory (.), and all of the directories beneath bin.
CLASS_PATH = -cp ".$(shell $(FIND) $(BIN_LOCATION) -type d -printf "$(CP_SEP)%p")"


# These three variables contain a bit of magic.
# RUN_DIRECTIVE is the name of the directive used to run the compiled source. It can be changed and is invoked as `make X Y=...` where X is the run directive name and Y is NAME.
# NAME is the name of the variable denoting the location of the class that contains main.
# RUN_NAME is the contents of the variable named by NAME. Argument passing courtesy of Jakob Borg: stackoverflow.com/questions/2214575/passing-arguments-to-make-run

# The purpose of this abstraction is two-fold:
# 1. Suggestions to the user are always accurate. This can be seen when calling RUN_DIRECTIVE without NAME as an argument.
# 2. As a minor convenience, users of this makefile can rename RUN_DIRECTIVE and or NAME to suit their needs.
# The general call is: make RUN_DIRECTIVE NAME=thePackage.theClass
# For example: make foo bar=thePackage.theClass
# where RUN_DIRECTIVE = foo, and NAME = bar.
RUN_DIRECTIVE = run
NAME = MAIN
RUN_NAME = ${$(NAME)}

SRC_FILES = $(shell $(FIND) $(SRC_LOCATION) -type f -name '*.java')
BIN_FILES = $(shell $(FIND) $(BIN_LOCATION) -type f -name '*.class')

default:
		@$(COMPILER)\
		$(COMPILER_FLAGS)\
		$(OPTIONAL_FLAGS)\
		$(SRC_FILES)

$(RUN_DIRECTIVE):
# We have a conditional here because javac's output when missing the main class' path is very unfriendly.
# The new lines must go at the front of the echo statement, adding them at the end created a single space before each line. Cause unknown.
# Note that we can't use RUN_NAME in the explanation because it doesn't exist (by definition, this is why we're running the else clause).
		$(if $(RUN_NAME), @$(EXECUTION) $(CLASS_PATH) $(RUN_NAME),\
		@$(ECHO) "Please provide the location of the main class:" \
		"\n$(MAKE) $(RUN_DIRECTIVE) $(NAME)=thePackage.theClass" \
		"\nYou do not need to add $(BIN_LOCATION) to the front" \
		"\nEnsure you do not add .class to the end of the main class location" \
		"\nIf your class is not in a package, write $(NAME)=theClass instead")

clean:
	$(RM) $(BIN_LOCATION)*
