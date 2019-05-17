# NLTK

NLTK (Natural Language Toolkit)은 파이썬에서 자연어 처리를 위한 패키지입니다.

말뭉치는 nltk의 서브패키지로 `download`로 설치할 수 있습니다.

다음은 서브패키지를 다운로드 합니다.

```python
import nltk
nltk.download('서브패키지이름')
from nltk.서브패키지이름 import *
```

nltk의 기본 API는 다음과 같습니다.

- `fileids()`:  코퍼스 파일을 얻는다.
- `fileids([categories])`: 카테고리에 해당하는 코퍼스 파일들을 얻는다.
- `categories()`:  코퍼스의 카테고리를 얻는다.
- `categories([fileids])`: 이들 파일에 해당하는 코퍼스의 카테고리를 얻는다.
- `raw()`: 코퍼스의 원시 내용을 얻는다.
- `raw(fileids=[f1, f2, f3])`:  지정된 파일의 원시 내용을 얻는다.
- `raw(categories=[c1, c2, c3])`:  지정된 카테고리의 원시 내용을 얻는다.
- `words()`: 전체 코퍼스의 단어를 얻는다.
- `words(fileids=[f1, f2, f3])`: 지정한 `fileids`의 단어를  얻는다.
- `words(categories=[c1, c2, c3])`: 지정한 `categories`의 단어를  얻는다.
- `sents()`: 전체 코퍼스의 문장을 얻는다.
- `sents(fileids=[f1, f2, f3])`: 지정한 `fileids`의 코퍼스의 문장을 얻는다.
- `sents(categories=[c1, c2, c3])`: 지정한 `categories`의 코퍼스의 문장을 얻는다.
- `abspath(fileid)`:  디스크에서 해당 파일의 경로를 얻는다.
- `encoding(fileid)`:  해당 파일의 인코딩을 얻는다.
- `open(fileid)`:  코퍼스 파일을 읽기 위해 스트림을 연다.
- `root`:  코퍼스가 설치된 루트 경로를 얻는다.
- `readme()`: 코퍼스의 `README` 텍스트 파일 내용을 얻는다.

말뭉치 (corpus)는 자연어 분석을 위해 만든 샘플 문서 집합입니다. 말뭉치의 집합은 corpora라고 하며, 단순한 집합부터 품사, 형태소 등의 태그를 부여한 것까지 다양합니다.

`gutenberg` 말뭉치는 저작권이 말소된 문학작품들이며 다음 코드로 목록을 볼 수 있습니다.

```python
nltk.corpus.gutenberg.fileids()
```

```
['austen-emma.txt',
 'austen-persuasion.txt',
 'austen-sense.txt',
 'bible-kjv.txt',
 'blake-poems.txt',
 'bryant-stories.txt',
 'burgess-busterbrown.txt',
 'carroll-alice.txt',
 'chesterton-ball.txt',
 'chesterton-brown.txt',
 'chesterton-thursday.txt',
 'edgeworth-parents.txt',
 'melville-moby_dick.txt',
 'milton-paradise.txt',
 'shakespeare-caesar.txt',
 'shakespeare-hamlet.txt',
 'shakespeare-macbeth.txt',
 'whitman-leaves.txt']
```

이중 제인 오스틴의 첫번째 항목 `austen-emma.txt` 문서를 보겠습니다.

```python
emma_raw = nltk.corpus.gutenberg.raw("austen-emma.txt")
print(emma_raw)
```

```
[Emma by Jane Austen 1816]

VOLUME I

CHAPTER I


Emma Woodhouse, handsome, clever, and rich, with a comfortable home
and happy disposition, seemed to unite some of the best blessings
of existence; and had lived nearly twenty-one years in the world
with very little to distress or vex her.

She was the youngest of the two daughters of a most affectionate,
indulgent father; and had, in consequence of her sister's marriage,
been mistress of his house from a very early period.  Her mother
had died too long ago for her to have more than an indistinct
remembrance of her caresses; and her place had been supplied
by an excellent woman as governess, who had fallen little short
of a mother in affection.

중략...

The wedding was very much like other weddings, where the parties
have no taste for finery or parade; and Mrs. Elton, from the
particulars detailed by her husband, thought it all extremely shabby,
and very inferior to her own.--"Very little white satin, very few
lace veils; a most pitiful business!--Selina would stare when she
heard of it."--But, in spite of these deficiencies, the wishes,
the hopes, the confidence, the predictions of the small band
of true friends who witnessed the ceremony, were fully answered
in the perfect happiness of the union.


FINIS
>>>
```

자연어 문서를 처리하기 위해서는 문서를 작은 단위로 나눠야 하고, 이 작은 단위를 토큰이라고 하며, 이 작업을 `tokenize`라고 합니다. 다음은 엠마 문장의 문장을 `tokenize()` 합니다.

```python
from nltk.tokenize import sent_tokenize
print(sent_tokenize(emma_raw))
```

```
['[Emma by Jane Austen 1816]\n\nVOLUME I\n\nCHAPTER I\n\n\nEmma Woodhouse, handsome, clever, and rich, with a comfortable home\nand happy disposition, seemed to unite some of the best blessings\nof existence; and had lived nearly twenty-one years in the world\nwith very little to distress or vex her.', "She was the youngest of the two daughters of a most affectionate,\nindulgent father; and had, in consequence of her sister's marriage,\nbeen 
중략 ...
the\nparticulars detailed by her husband, thought it all extremely shabby,\nand very inferior to her own.--"Very little white satin, very few\nlace veils; a most pitiful business!--Selina would stare when she\nheard of it."', '--But, in spite of these deficiencies, the wishes,\nthe hopes, the confidence, the predictions of the small band\nof true friends who witnessed the ceremony, were fully answered\nin the perfect happiness of the union.', 'FINIS']
```

다음은 워드를 토크나이즈 합니다.

```python
from nltk.tokenize import word_tokenize
word_tokenize(emma_raw[0:500])
```

```
['[', 'Emma', 'by', 'Jane', 'Austen', '1816', ']', 'VOLUME', 'I', 'CHAPTER', 'I', 'Emma', 'Woodhouse', ',', 'handsome', ',', 'clever', ',', 'and', 'rich', ',', 'with', 'a', 'comfortable', 'home', 'and', 'happy', 'disposition', ',', 'seemed', 'to', 'unite', 'some', 'of', 'the', 'best', 'blessings', 'of', 'existence', ';', 'and', 'had', 'lived', 'nearly', 'twenty-one', 'years', 'in', 'the', 'world', 'with', 'very', 'little', 'to', 'distress', 'or', 'vex', 'her', '.', 'She', 'was', 'the', 'youngest', 'of', 'the', 'two', 'daughters', 'of', 'a', 'most', 'affectionate', ',', 'indulgent', 'father', ';', 'and', 'had', ',', 'in', 'consequence', 'of', 'her', 'sister', "'s", 'marriage', ',', 'been', 'mistress', 'of', 'his', 'house', 'from', 'a', 'very', 'early', 'period', '.', 'Her', 'mother', 'had', 'died', 't']
```

다음은 정규식을 사용하여 토크나이즈 합니다.

```python
from nltk.tokenize import RegexpTokenizer
retokenize = RegexpTokenizer("[\w]+")
retokenize.tokenize(emma_raw[0:1000])
```

```
['Emma', 'by', 'Jane', 'Austen', '1816', 'VOLUME', 'I', 'CHAPTER', 'I', 'Emma', 'Woodhouse', 'handsome', 'clever', 'and', 'rich', 'with', 'a', 'comfortable', 'home', 'and', 'happy', 'disposition', 'seemed', 'to', 'unite', 'some', 'of', 'the', 'best', 'blessings', 'of', 'existence', 'and', 'had', 'lived', 'nearly', 'twenty', 'one', 'years', 'in', 'the', 'world', 'with', 'very', 'little', 'to', 'distress', 'or', 'vex', 'her', 'She', 'was', 'the', 'youngest', 'of', 'the', 'two', 'daughters', 'of', 'a', 'most', 'affectionate', 'indulgent', 'father', 'and', 'had', 'in', 'consequence', 'of', 'her', 'sister', 's', 'marriage', 'been', 'mistress', 'of', 'his', 'house', 'from', 'a', 'very', 'early', 'period', 'Her', 'mother', 'had', 'died', 'too', 'long', 'ago', 'for', 'her', 'to', 'have', 'more', 'than', 'an', 'indistinct', 'remembrance', 'of', 'her', 'caresses', 'and', 'her', 'place', 'had', 'been', 'supplied', 'by', 'an', 'excellent', 'woman', 'as', 'governess', 'who', 'had', 'fallen', 'little', 'short', 'of', 'a', 'mother', 'in', 'affection', 'Sixteen', 'years', 'had', 'Miss', 'Taylor', 'been', 'in', 'Mr', 'Woodhouse', 's', 'family', 'less', 'as', 'a', 'governess', 'than', 'a', 'friend', 'very', 'fond', 'of', 'both', 'daughters', 'but', 'particularly', 'of', 'Emma', 'Between', '_them_', 'it', 'was', 'more', 'the', 'intimacy', 'of', 'sisters', 'Even', 'before', 'Miss', 'Taylor', 'had', 'ceased', 'to', 'hold', 'the', 'nominal', 'office', 'of', 'governess', 'the', 'mildness', 'o']
```











