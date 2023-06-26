# Jupyter Notebook Snips

Wordcloud
===

Generate a word cloud with a set of words.

```
import matplotlib.pyplot as plt

try:
    from wordcloud import WordCloud
except:
    !pip3 install wordcloud

from wordcloud import WordCloud

# words to use
text = "Each word is used in the word cloud"

wordcloud = WordCloud(width=1024, height=300, background_color='black').generate(text)

# show word cloud
plt.figure(figsize=(10, 5))
plt.imshow(wordcloud, interpolation='bilinear')
plt.axis('off')
plt.show()
```