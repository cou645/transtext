# transtext
transtext is a drop in inclusive replacement for GNU-gettext. It is inclusive in that translations are not exclusive to a TEXTDOMAIN.

transtext currently uses trans script to accomplish text string translations, either for interface labels or bodies of text. However any translation engine API could feasibly by used.

The transtext system hashes the original text string and stores the retrieved translation using the hash. 
This is more efficient than the GNU-gettext system which stores translations according to TEXTDOMAINs, which is the scripts name.
This means that a retreived translation can be re-used by any number of scripts because the hash for a given string will always be the same. 

transtexts are stored according to language sub directories for convenience, but it's not necessary.

# for example:
# determine target language from system locale code
[ ! "$lng" ] && lng=$(locale | grep LANG= | cut -f -d'_') || lng="$lng"

# get the hash of the original string to use as the name of the saved translated string
HANDLE=$(echo "hello world"  | md5sum | awk '{print $1}')

# use trans script or an API command for any translation engine, to retrieve translation from one of its supported engines (google, bing, yandex), also get audio of translation for audible labels function
WORK=$(echo -e "$1" | trans -e google -no-auto -b -tl "$lng" -download-audio-as /usr/share/locale/transtext/$lng/$HANDLE.mp3 &)

# output translated string to the gettext call in the script/app
echo "$WORK"

# save the translated string in target language directory with the hash $HANDLE and .transtext extension
[[  ! -f /usr/share/locale/transtext/"$lng"/"$HANDLE".transtext ]] && echo -e "$WORK" > /usr/share/locale/transtext/"$lng"/"$HANDLE".transtext

# End of example
The script first hashes the input text string and searches for a saved transtext translation to use, before attempting to get a translation from a translation server.

A transtext function is included to translate the labels of a review editor which is included in the gettext function, this is to avoid an infinite loop.
