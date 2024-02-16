---
layout: post
title: "Tokenization in Nepali"
author: "Shreeya Dhakal"
categories: [nepali-grammar, nlp-fundamentals]
tags: [tokenization, nepali-grammar]
image: tokenization.jpg
---

Tokenization is generally the first step in text analysis applications. It is the process of splitting the given string into units, called tokens. A token is a sequence of characters, usually a word or sentence that is semantically significant for text analysis. Tokenization is a language-specific task; for instance, a language like Chinese that has no space between words requires a language-specific approach towards tokenization. However, Nepali, being a language where words are separated by white spaces, the general boundary-defined tokenization method can be used.

## Sentence-level Tokenization

In cases where we want our tokens to be sentences, our possible token boundary is either पूर्णविराम(।) or प्रश्न चिन्ह(?) or विस्मयादिबोधक(!). So, by splitting the given text at पूर्णविराम(।) or प्रश्न चिन्ह(?) or विस्मयादिबोधक(!), sentence-level tokenization can be achieved.

पूर्णविराम(।) in Nepali is equivalent to a full stop (.) in English.

```
# Split at ?, । or !
return re.split('(?<=[।?!]) +', text)

INPUT: 
परिश्रम नगरी हुन्छ? परिश्रम सफलताको एक मात्र बाटो हो। जो परिश्रम गर्छ, उही सफल हुन्छ। अब त परिश्रम गर्छौ नि? नगरी कहाँ हुन्छ त!

OUTPUT:
['परिश्रम नगरी हुन्छ?', 'परिश्रम सफलताको एक मात्र बाटो हो।', 'जो परिश्रम गर्छ, उही सफल हुन्छ।', 'अब त परिश्रम गर्छौ नि?', 'नगरी कहाँ हुन्छ त!']
```

You can find out more about punctuation marks in Nepali at: [Punctuation in Nepali] (https://nepalgo.tumblr.com/post/71951951192/punctuation-marks).

## Word-level Tokenization

In languages that have words separated by blank spaces, the token boundary for word-level tokenization is the blank/white space. Nepali is one such language, so word-level tokenization in Nepali can be achieved by stripping tokens at those white spaces. However, it is not as simple as it looks, especially when dealing with punctuations. Some punctuation is easy to handle. All that you need to do is replace them with a white space and you are good to go.

Cases involving hyphen (-) and colon (:) need to be handled differently.

## Replacing Punctuations with White Space

The following is the list of punctuations that can be handled by simply replacing them with a white space.
```
,
)
(
{
}
[]
!
‘
’
“
”
:-
?
।
/
—
```

```
INPUT:
राम आयो र भन्यो, "दाइ र दिदि, यति दिन कता हुनुहुन्थ्यो?" तब, दाइ/दिदिले केहि नबोली आफ्नो (बाटो) लाग्नुभयो।

OUTPUT:
['राम', 'आयो', 'र', 'भन्यो', 'दाइ', 'र', 'दिदि', 'यति', 'दिन', 'कता', 'हुनुहुन्थ्यो', 'तब', 'दाइ', 'दिदिले', 'केहि', 'नबोली', 'आफ्नो', 'बाटो', 'लाग्नुभयो']
```

## Hyphen (-)

In Nepali, a Hyphen (योजक चिन्ह) is used in linking word pairs: opposite, analogy or similar, together. In such cases, the hyphen is considered as a part of the token itself.

```
जीवन-प्रक्रिया

आ-आफ्नो

स-सानो
```

However, in many online texts, hyphens are also being used in place of one of the निर्देशक चिन्ह: dash (—). In this case, there are two ways in which a hyphen is used.

For the first case, the hyphen occurs independently.

```
# Independent hyphen i.e. not attached to the word
आक्रोश - घाम
```
In case, as shown above, the hyphen is simply replaced with a white space. 

In the second case the hyphen is attached to the end of a word. In such cases the hyphen is tokenized with the word as a part of the word and then all the hyphens that occurs as the last character in the token is removed.

```
if word[len(word) - 1:] == '-':
    words.append(word[:len(word) - 1])

```

```
INPUT: 

आ-आफ्नो बाटो लागे- भन्दै - श्यामपनि आफ्नो बाटो हिड्यो।

OUTPUT:

['आ-आफ्नो', 'बाटो', 'लागे', 'भन्दै', 'श्यामपनि', 'आफ्नो', 'बाटो', 'हिड्यो']
```

## Colon(:)

Colon in Nepali, can occur as a part of word or as निर्देशक चिन्ह in place of a dash(—). Colon, which is a part of word can appear either in between or at the end of the word. Some of such words are listed below.
```
दु:ख
नि:स्वार्थ
मूलत:
प्रथमत:
क्रमश:
```
For colon that appear as in place of dash, they have two cases similar to the ones discussed above in hyphen. The first case is handled in the same way the first case of hyphen was handled i.e. by replacing the punctuation with a white space. However, handling the second case where colon appears as the last character of the word is a little tricky because it is difficult to identify if is a part of the word or is used in place of a dash. Since, a handful of words in Nepali end with colon, the current tokenizer uses a lexicon of such words to to understand if the end-of-word colon is a part of the word or is it used in place of a dash. 

The colon is replaced with a white space if the word doesn't exist in the lexicon of words.
```
if word[len(word) - 1:] == ':' and word not in colon_lexicon:
    words.append(word[:len(word) - 1])
else:
    words.append(word)
```
```
INPUT:

यस्ता छन्: प्रथमत: नि:स्वार्थ।

OUTPUT: 

['यस्ता', 'छन्', 'प्रथमत:', 'नि:स्वार्थ']
```

## Period(.)

Period is another punctuation used in Nepali. Unlike in English where a period can be used in two cases: ending a sentence i.e. as a fullstop and as a part of abbreviation, in Nepali it is used for latter only. 
```
गा.वि.स.
डा.
कि.मी.
```
So, for tokenization of Nepali texts, period is considered as a part of the token itself.

