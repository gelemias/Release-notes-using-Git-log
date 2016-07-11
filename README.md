# Release notes using Git log
Simple bash script to generate Release notes using git commit messages

This script is meant to be used with an specific format:

**[TYPE] MESSAGE**

Types commonly used are chore, docs, feat, fix, refactor, style, or test.


It takes every commit message using a 'tag' such as [TAG] at the beginning of the message and divides the output by tags

i.e: _[FEATURE] Implemented searchView on MainView._


Only these commit messages using this specific format will be used for the release notes, the differences between the new version and previous version is based on the commits between the last tag and the current head.


---

this specific example requires build number and version, which for example, iOS or OS X:

```bash
BUILD_NUM=$(/usr/libexec/PlistBuddy -c "Print CFBundleVersion" "${PROJECT_DIR}/${INFOPLIST_FILE}")
BUILD_VER=$(/usr/libexec/Plistbuddy -c "Print CFBundleShortVersionString" "${PROJECT_DIR}/${INFOPLIST_FILE}")
```

```bash
FILE='ReleaseNotes.md'
branch=$(git branch | sed -n -e 's/^\* \(.*\)/\1/p')
LATEST_TAG=$(git describe --abbrev=0)

> $FILE
echo '## RELEASE NOTES - ' ${TARGET_NAME} $BUILD_VER.$BUILD_NUM >> $FILE
echo '' >> $FILE
echo '#### New in this version:' >> $FILE
git log $LATEST_TAG..HEAD --no-merges --grep="\[" --pretty=format:"+ %ad %x08  %s" -50 --date=short | sed '/^\s*$/d' >> $FILE
echo '' >> $FILE
echo '#### Previously released:' >> $FILE
git log INIT..$LATEST_TAG --no-merges --grep="\[" --pretty=format:"+ %ad %x08  %s" -50 --date=short | sed '/^\s*$/d' >> $FILE

```

<br>
## OUTPUT

### RELEASE NOTES - Project Name 1.0.131
-----------------------------------------------

#### New in this version:
+ 2016-03-04 [FEATURE] Added log out in settings view
+ 2016-03-03 [FEATURE] Added a first stage layout for settings view
+ 2016-02-28 [REFACTOR] Enhanced sign up view
+ 2016-02-27 [STYLE] Started with Signup look and feel
+ 2016-02-27 [FEATURE] Translation between login and signup views
+ 2016-02-26 [FIX] Logging mismatch 

#### Previously released:
+ 2016-02-25 [REFACTOR] Login sign up time tweaked 
+ 2016-02-24 [FEATURE] Added a button for submitting the registration + error handling
+ 2016-02-24 [FIX] Error handling when many errors occur at the same time
+ 2016-02-24 [FIX] Login-signup error handling
+ 2016-02-23 [STYLE] Added proper image to re-password textfield
