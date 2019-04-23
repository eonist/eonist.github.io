This is a reference overview to common RegEx patterns<!--more--> 

```swift
NOTE: (\b becomes: \\b if you use the RegExp string pattern, other: \\w, \\W, \\D,\\d,\\A,\\s etc) (to match punctuation on can use [.])
NOTE: String expression in RegExp can be a little hard to compose, a good tip is to write the write a regular expression first and test if it works, then write it in a String expression and then tracing the string first to see if it matches the regular expression exactly 
NOTE: When testing with an regExp pattern in a loop remember to recreate the regExp pattern for every itteration
NOTE: To access individual groups in a match remember to not use the g (global flag)
NOTE: if you can try to be as precies as possibly (if you can use conditional backrefrencing then do it)
NOTE: if you ever need to "backward look" with a dynamic length, you simply cant, unless you reverse the string and "forward look", since
NOTE: forward look can have dynamic lengths
```

```swift
public static let nonLiteralPatternExample:String = "[a-z]{4,8}"
public static let literalPatternExample:String = "[a-z]{4,8}"
public static let extendedPatternExample:String = " 	(\\w|[_.\\-])+ 	@	 ((\\w|-)+\\.)+ 	\\w{2,4}+	 "//the x-flag enables spacing or tabbing in a pattern , for more readability
```
#### Flags:
```swift
/**
 * g	- global//finds every instance of (dont use global if you only need to find 1 match, global is more cpu intensive)
 * i	- ignore case (cae insensetiv) (not commonly used)
 * m	- multiline ^ and $ match next to embedded line terminators (Example in RegExpMatch.comments)
 * s	- dot matches newline (not commonly used)
 * x	- extends notion allows space in regular expression, (not commonly used)
 * - if you want to match a literal space use the backslash like: pattern = /a\ b/x, pattern.test("a b")//output: true
 */
```
#### Other:
```swift

/**
 * [a-z]- any lowercase character
 * [a-z0-9] any lowercase character or numbers
 * [A-z] any character ([a-zA-Z] also works)
 * [a-zA-z0-9_] any character or number - equivalent to \w
 * [A-z0-9_] any character or number - equivalent to \w 
 */
```
#### Meta characters (Characters Repetition): 
```swift
/**
 * .   - any character; (includes the newline character only when the s flag is set) [^~\0A\x0A\u20028\u2029] (a common problem with this meta char is that it also swallows the $ aka the end, so you cant trace for the $ which is very usefull) (also have dificulties using . inside [] brackets)
 * *   - match 0 or more occurrences of the preceding item (Greedy quantifier)
 * +   - match 1 or more occurences of the preceding item (Greedy quantifier)
 * ?   - match 0 or 1 occurrences of the preceding item (In other words: subseeding character is optional) (Example in RegExpMatch.as - b tag) (this can also work on groups see RegExpHelp.optionalEnding)
 * *?  - Match 0 or more times, but as few times as possible (Lazy quantifier) (Example in RegExpMatch.as - b tag) (if you can use just * then use it) (IMP: if the character after is difficult to predict. use the * instead)(IMP: i.e \w*?\d{3}?$  in this example the lazy quantifier will trace all the way to $ if 3 digits subseed doesnt subseed the word)(The trick with the greedy quantifier is that it needs to go all the way to something that forsure can be defined, like $, the great thing is that it can be followed by more greedy quantifier but in the end it has to go to something that can be for sure defined)
 * +?  - Match 1 or more times, but as few times as possible (Lazy) (use this instead of the *? if you know there will be more than 1 char)
 * ??  - Match 0 or more 1 time, but as few as possible
 * \   - Escapes a metacharacter or metasequence so its interpreted literally like so \\ (This applies only when you use string RegExp patterns)
 */
```
#### Groups:
```swift
/**
 * ()  - A group subpattern and capture submatch into backreferences: \1, \2... and $1, $2,... (backrefrence ranges can be acces via: $1-$3) (Example of backrefrence replacing: RegExpModifier.conformUsTeleNr)
 * []  - A character class
 * [A-D] Matches any character between A and D
 * [a-g] Matches any character between a and g  
 * [A-z] Matches any character between a and z, as well as between A and Z
 *
 * [a]
 * [abc]-	any one of a, b and c (can also be written [a,b,c])
 * [Aa]bc	either of Abc and abc
 * [abc]+	any (nonempty) string of a’s, b’s and c’s (such as a, abba, acbabcacaa)
 * [^abc]+	any (nonempty) string which does not contain any of a, b and c (such as defg) -> [^,;]+ will match any string that does not contain a comma and semi colon
 * (|)  - Either the item on the left or the item on the rigth and works with multiple i.e (a|b|c|d)  (use brackets to enclose the or operator: (|)  )(Rember when using the OR operator that you must try the least possible first) remeber you can use ? and * to make items optional (Example: RegExpMatch.validIp)
 */
```
 
#### Metasequences: (character classes or class like constructs, Repetition Characters)
```swift
/**
 * [a,b,c]- any of the characters a , b or c (set of characters) (can also be written [abc])
 * [^a,^b,^c] - none of the characters a,b or c
 */
```
#### Repetition modifier:
```swift
/**
 * ..{n}  - Match exactly n occurences of the preceding item (\n{3} matches 3 numerals)
 * ..{n}? - Match at least n times but as few as possible
 * ..{n,} _ Match atleast n or more ocurrences of the preceding item (Greedy quantifier)
 * ..{x,y}- Match Between x and no more than y occurences of the preceding item
 * ..{x,y}?-Match Between x and no more than y occurences of the preceding item, and as few as possible
 */
```

#### Character matching:
```swift
/**
 * \A   - The start of the string (you can also use ^) (You cant use ^ inside a [] group)
 * \b   - non-character border (“word” boundary) (the border between a word character) (a-z, A-Z, 0-9 or _) or \w and non-word character \W including the start and end of a string or \x08 (anchor) between whitespace and a character
 * \B   - non-word-boundary (not a “word” boundary) or The border between two word characters or two non - word character ie : <ab> <12> <,;> (between a and b) 
 * \d   - Any digit or [0-9] (Example: \d\d	any two decimal digits, such as 42; same as \d{2})
 * \D   - Any non-digit [^~0-9]
 * \n	- Newline or \x0A (Often static text generated by code is line broken with \n)
 * \r	- Return or \x0D (Often user input text in textBoxes is linebroken with \r)
 * \f	- Form feed character \u000C
 * \t	- Tab or \x09 (horizontal)
 * \v	- Vertical tab \u000B
 * \s	- Any whitespace same as [\f\n\r\t\v]
 * \S	- Any non-whitespace same [^\f\n\r\t\v]
 * \040 - The exact space character " "
 * \unnnn The unicode character represented by the character code nnnn
 * \w	- Any word character or [a-zA-z0-9_] or the following a-z, A-Z, 0-9, _
 * \W	- Any non-word character or [^a-zA-z0-9_] or none of the following a-z, A-Z, 0-9, _
 * \xnn - The character represented by the ASCII value nn
 * \z	- The end of the string (you can also use $)
 * \.	- the literal character .  (\\. when using the RegExp Constructor)
 */
```
#### Anchors:
```swift
/**
 * ^    - The start of a string (or start of a line when in multiline match mode /m) (you can also use \A)
 * $    - The end of a string (or the end of a line when the m-flag is set) (you can also use \z)
 */
```

#### Look-around groups:
```swift
/** 
 * ?=	- Positiv lookahead  (the subseeding pattern must apear, but is not included in the match) //if you want to retrive the filename minus the file extension
 * ?! 	- Negative lookahead (the subseeding pattern must not apear)//if you want to exclude the subpatteren
 * ?<=  - Positiv lookbehind (the preseeding pattarn must apear, but is not included in the match) 
 * ?<!  - Negativ lookbehind the preceding pattern must NOT appear, and is not included in the fiinal match (Example: RegExpParser.nonUsPrices.as)
 * IMORTANT NOTE: Lookahead patterns may be variable length; they may contain . and +, for example, so as to be highly dynamic.
 * IMORTANT NOTE: Lookbehind patterns, on the other hand, must generally be fixed length. This is a restriction imposed by almost all regular expression implementations.) (If you have a fixed len lookahead, a hack may be to reverse the string so that that you look ahead when you really look behind, this way you can have non-fixed lookbehinds)
 */
```

#### Embedded conditions (Grouping, capturing, conditional and control)
```swift
/**
 * ..? 		- Matches the previouse expression if it exists (same as ? matches 0 or 1)
 * ..*		- Matches previouse expressions if they exists (same as * matches 0 or more)
 * ?(..)    - checkes if a backreference group exists I.e: ?(\1) (if the first group exist then return true) (?(backreference)true|false). (see regexp condional examples) 
 * ?=  		Match text ahead if it exists (but not consume meaning the cursor doesnt move to next chracter THIS IS IMP) (Note at the start of a string its better to use ?<=, but remember backwardlooking must be fixedsize)
 * ?<= 		MAtch behind it if it exists (but not consume) Example (?<=-) matches "-" but does not consume  (the expression must match exactly, it cant be left up tp chance)
 * 			(this function is strange try different tests) (has some problems with ^(?<=..), maybe dont use the ^)
 * 			Examples: RegExpModifier.removeLeftAndRightWhitespace(text);
 * (?:...)	- (indicate a non-capturing group that doesn't generate backreferences) Non-capturing group - everything inside this must be matched but is not included in the end match and the group is not referable (great when using associativ functionality) (skips the subpattern when using the exec function, see example of this in RegExpParser.computerDate)
 * (...|...)  - Try subpatterns in alternation (Rember when using the OR operator that you must try the least possible first) (something|else) matches something or else (You should wrap the alternatives and or operater betwen "(" and ")" like so: /(\w+|\d+)$/g or else the $ wont be to the option group but to the last option) (use non-capturing group if you dont want to backref the group)
 * (?P<some name>..) - assigns an associative name to the array (You must use .match to find 1 instance, and  .exec to find many instances)
 */
```
#### Speacial characters:
```swift
/**
 * Note: if you use the RegExp Constructor, escape metaSequences like so: "\\d"
 * Note: If you want to math the literal backslash character you can escape it so: \\
 * escaping ] character so: \] @example: /[a-z\\]/ matches any lowercase or the backslash character
 */
```
#### Whitespace metacharacters
```swift
/** 
 * [\b] Backspace
 * \f formfeed
 * \n Line feed
 * \r Carriage return
 * \t Tab
 * \v Vertical tab
 */

```

#### Methods:
```swift
/** 
 * string.search(pattern)
 * Match pattern against the string returning either the character position of the start of the first matching substring -1.
 *
 * string.replace(pattern, replacement)
 * The replace method searches the string for a match of pattern and replaces the matched substring with replacement
 * if pattern has global mode set, then all matches of pattern are replaced. The replacment string may have 
 * $n constructs that are replaced with the matched text of the nth capture group pattern (the $n may be refered to many times)
 * Tips: When manipulating text for reformatting, it is often useful to break the text into lots of little subexpressions so as to have greater control over that text.
 * 
 * string.match(pattern)
 * Match pattern against the string returning either an array or -1. Element 0 of the array contains the full match 
 * additional elements contain submatches from capturing groups. In global mode , the array contains all matches of 
 * pattern with no capture groups.
 * 
 * string.split(pattern, limit)
 * Return an array of strings broken around pattern. if limit, the array contains at most the first limit substrings 
 * broken around pattern. If pattern contains capture groups, captured substrings are returned as elements after each split substrings
 * 
 * pattern.exec(string)
 * Search text and return an array of strings if the search succeeds and null if it fails. Element 0 of the array 
 * contains the substring matched by the entire regular expression. Additional elements correspond to capture groups.
 * Note: If the global flag is set then the lastIndex is set to the character position after the match or zero if there is no match. 
 * Successive exec() or test() calls will start at lastIndex. Note thatlastIndex s a property of the regular expression, not the 
 * string being searched. You must reset lastIndex manually if you are using a RegExp object in global mode to search multiple settings.
 * 
 * pattern.test(pattern)
 * Return true if the RegExp object matches string. The test() method behaves in the same way as exec() when used in global mode: succesive
 * calls starts lastIndex even if used on different strings.
 * 
 */
```
#### Special characters need to be escaped

```swift
/**
 * $  = \$
 * /  = \/ - Escape forward slash with a backslash like so \/
 * * = \*  and \\* when using string pattern
 */
```

#### These characters does not need to be escaped:
```swift
/**
 * %
 * #
 * <
 * >
 */
```
#### Backlash characters:
```swift
/**
 * To match any of the MetaCharacters you need to escape them with the backslash: (\$ becomes: \\$ if you use the RegExp string pattern)
 * \* \$ \( \) \* \+ \. \? \[ \\ \^ \{ \|\-
 */

```

#### Back referencing test:
 
 ```swift
/**
 * "6.6".test("\d(\.?)((?1)\d)")//6.6 The important part about this example is that group 1 can be true or false. if a group can only be true this example wont work
 */
```
