# DART_CASES
DART CASE directories from CESM+DART experiments.

This is a way to archive the experiment setups for publication purposes
and curate what happens during the continued cycling of a CESM experiment.
Some (most?) things are expected to stay static, but better to be safe than confused.

There is a complication that arises from trying to create a git directory
from an existing CESM CASE directory, and there is also a complication that
arises from trying to create a CESM CASE directory from an existing git directory.
This is only an issue for existing CASEs which are not under git control.
Instructions to accomplish that are near the end of this README.

The following strategies are based on making the 'main' branch of the git repository
have no CASE files in it, and each experiment/case will be a unique branch name 
that will reflect the CESM 'CASE' name.


## Terminology

| Word | Meaning|
 :--------------- |:----------------------------------------------------------|
|DART\_CASES | the github repository for archiving assimilation setups|
|CASE       | your CESM CASE name, which is the name of the assimilation |
|           | and the git branch. |
|CASEROOT   | the directory in which CESM builds CASE and from which |
|           | assimilation jobs are launched.  '<cases>/$CASE' below. |
|compset    | a character string which is fed to CESM and specifies |
|           | which components will be active, data, and stub, |
|           | and the model version to use for each component |

## Create a New CASE Under git(hub)

Preliminaries
0. If you're not an employee in NSF NCAR's Data Assimilation Research Section,
   fork https://github.com/NCAR/DART\_CASES.git to your github.
1. In a directory where you have access to git(hub) and enough space,
   clone the DART\_CASES repository into a directory name which will contain multiple CASEs.
   In CESM this is usually called 'cases', but it's your choice.  (I chose Exp\_git).
   > git clone git@github.com:NCAR/DART\_CASES.git ${CASE}
2. Edit <cases>/list\_of\_files\_to\_commit.csh (or rewrite in your favorite language)
   to specify which files should be under git control.  See comments in that file,
   which is an F compset (CAM) example, about how to choose them.

Production
3. In <your\_DART>/models/<your\_model>/shell\_scripts/<your\_setup\_script>
   1. Set the CASE name for this assimilation.
      If you're reusing a git-controlled name, do the appropriate cleaning
      of the existing git branch, which might mean just
      > git rm -r $CASE
      > git branch -d $CASE
   2. Specify that this case will be built in <cases>.
   3. add lines near the end to put CASE under git control:
      > (cd $CASEROOT)
      > git checkout -b ${CASE}
      > ../list\_of\_files\_to\_commit.csh
      > git commit -m "First commit of files in case $CASE "
      This will create a *branch* in your <cases> clone which has the name $CASE.
4. Run the setup script
   This will create a *directory* in your <cases> clone which has the name $CASE.
   Follow the instructions about checking the setup.
5. If needed, in the $CASEROOT directory, edit input.nml, \*.xml, etc.
   to define your assimilation job correctly.
   Add, commit, and push these to the experiment git(hub) branch.
6. Submit the job
7. If git-controlled files need to be changed for future DA cycles,
   add, commit, and push them to the (correct) branch.

## Variation
You can clone a DART\_CASES for each CASE, which sidesteps needing to
switch back and forth between branches when running multiple experiments.
Each CASE should be in a subdirectory (named $CASE) of the clone's top directory,
so that when they are all pushed to DART\_CASES it will be extra clear
which branch is currently checked out; there will be a directory
which has the CASE name.


## Put an existing CASE under git(hub) control in its own directory

1.  Change the CASE directory name (made by CESM) to ${CASE}\_orig.
2.  Clone the DART\_CASES repository into ${CASE}\_git.
3.  In ${CASE}\_git make a new branch with the name ${CASE} and check it out.
4.  Copy everything from ${CASE}\_orig into a new subdirectory ./${CASE} 
5.  Compare this copy with ${CASE}\_orig
6.  Make ${CASE}\_orig read-only, so that you don't accidentally use it.
7.  `git add` the files that should be archived to the git staging area.  You can use a variant of list\_of\_files\_to\_commit.csh
8.  Commit them (with a useful message)
9.  Push the contents of the local git repository to GitHub. There is a special syntax to push the new branch to the GitHub repository when the branch does not exist yet in the "origin".
10.  Ongoing; add, commit, and push any changes to the CASE which should be archived.


## Tcsh example

```
[step 0]% set CASE = <your\_casename>

[step 1]% mv ${CASE} ${CASE}\_orig

[step 2]% git clone git@github.com:NCAR/DART\_CASES.git ${CASE}_git

[step 3]% cd ${CASE}_git
[step 3]% mkdir ${CASE}
[step 3]% git checkout -b ${CASE}

[step 4]% rsync -av ../${CASE}\_orig/ ${CASE}

[step 5]% diff -r ${CASE} ../${CASE}\_orig | less

[step 6]% chmod 644 ../${CASE}\_orig

% Edit input.nml and other files to specify the assimilation.
[step 7]% git add <files\_you\_want\_to\_archive>
% See ./list\_of\_files\_to\_commit

[step 8]% git commit -m "First commit of files which must be archived"

[step 9]% git push -u origin ${CASE}
```

**Wild West method:**
   If the methods above aren't available to you, then hopefully you can use parts of them
   to set up your case under git control.
   There are too many degrees of freedom to describe here how to do it as a
   1 operating system command at a time.


