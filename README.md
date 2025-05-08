# Justifying War: Word Frequency Analysis and Russia's Changing Narrative on Ukraine
_Social Media Analysis / Group Project Submission / Thursday 8 May 2025_

  _Contributors: Tom-Erik Liljeroth FOSEID, Barbara KUZA-TARKOWSKA, Hans Iver Traaseth SKOGVANG, Mariia VAKULIK_



## Introduction

Russia is famous for its hybrid operations, particularly in the informational space. The country targets audiences inside and outside its borders to manipulate public opinion, and to strengthen support for its government's actions. These narratives are often described as 'propaganda', as they serve to shape the public perception of government policy. In this project, we have looked at a specific kind of propaganda, namely, that which seeks to justify Russia's invasion of Ukraine in 2022 and its ongoing occupation of Eastern Ukrainian regions.

Following the occupation and annexation of Crimea, Russia's rhetoric started to shift. The tone of communication with foreign partners evolved, growing more assertive and more overtly revisionist. Following the Ukraine War's outbreak in 2022, a number of false claims have been developed, distributed, and propagated to justify Russia's invastion of its neighbour:

- Ukraine is not a sovereign state, but part of a greater Russian civilisation.
- Ukraine is ruled by Nazis, and must be 'denazified' and 'demilitarised'.
- Ukraine commits genocide against its Russian speakers.

The aim of this project is to explore the reach of Russian propaganda quantitatively, through the official communications of the Ministry of Foreign Affairs (MFA). In this experiment, we will attempt to build on pre-existing knowledge by _measuring_ these political phenomena, rather than simply characterising them. In essence, we will seek to answer this question:

> How has Russia’s war rationale  evolved during the Ukraine War?



## Political background
Propaganda, in its simplest form defined as “the management of collective attitudes by the manipulation of significant symbols” (Lasswell, 1927, p. 627), comes in many different forms. It can be oriented towards foreign powers as a tool of policy legitimization, or to domestic audiences, to shape their perception of the competency and image of the polity’s leader.

Russia enjoys a rich and eventful history of such ‘management’, and its recent campaigns in the informational space are generally well-documented. Some researchers even dare to think quantitatively: Alieva et al. (2024), for example, use natural language processing methods to analyse the dissemination of misinformation relating to supposed Western and Ukrainian neonazism on Twitter. In the same vein, Lai et al. and Miskimmon (2024, 2017) employ these techniques in the Ukrainian context, examining the spread of Russian narratives of purported biological weapons labs in Ukraine, among other conspiracy theories relating to Western involvement in Ukrainian domestic affairs. They look to the 2013-2014 Euromaidan protests, the annexation of Crimea, and the intensification of hybrid measures in the informational domain leading up to the 2022 invasion of the country by Russia.



## The Dataset
The choice of dataset was driven by our intent to understand the development of Russian official rhetoric. While we initally struggled with finding one suitable to us – a collection of 1.000 articles from Factiva failed to capture rare keywords – we eventually discovered a more comprehensive collection of all Russian language news items published on the Russian Ministry of Foreign Affairs webpage. This dataset, retrievable from [this link](https://tadadit.xyz/datasets/2024/russian_institutions_2024/mid.ru_ru_2024/), was compiled by researcher Giorgio Comai as part of a project supported by the Italian Ministry of Foreign Affairs. The full corpus covers the period from January 2, 2003, to December 31, 2023, with a total of 56,203 articles. A corresponding dataset of English language Russian MFA news items is also available, but is significantly smaller than the Russian language corpus. Given the group's familiarity with Russian language, we chose the original language version. 



## The Code
The code used for running Spacy’s Natural Language Processing (NLP) - while simple - has gone through many iterations. Initially, the code processed the entire dataset of 56,203 articles, totalling over 20 million words – a volume of data that led to some computational problems. Simply running the NLP pipeline took several hours, even when using the smallest Russian language pipeline offered in the Spacey library (ru_core_news_md). Every time the Python kernel stopped, the process had to be restarted, causing the time involved in exploring the dataset and tinkering with the code to draw out significantly. 

To address this, we tried the in-built pickle module that comes with Python. Given that it produced a 14 gigabyte .pkl file that then required a multi-hour process to be unpacked on every run of the Python kernel, the situation had actually got worse. It also led to a sequence of errors in the rest of the code, producing more headaches. In sum, pickling created more issues than it solved.

Processing only a 10-year slice of the dataset proved to be much more practical, giving us a final time frame of 2013-2023. For the purposes of our project, we didn’t need the data in the preceding decade. This virtually cut the data volume in half – drastically reducing processing time – allowing us to finally begin exploring the data. In the end, our dataset spans 28,706 individual articles and 14,004,206 words.

In order to establish that the dataset was indeed readable and that the Russian NLP pipeline had successfully lemmatized and tokenized the data, the finished code contains multiple lines designed to verify this. Lemmatizing is a process that is particularly important given the morphology of the Russian language, in which one can find upwards of 50 inflections (variations) of a single word. Faulty lemmatization could therefore provide a drastically lower count of our candidate words. As demonstrated in the following code output, this was done successfully. 

```
  for token in nlp_doc[:10]:
      print (token,'->',token.lemma_)

#OUTPUT:
  СООБЩЕНИЕ -> сообщение
  ДЛЯ -> для
  СМИ -> сми
  В -> в
  ходе -> ход
  состоявшегося -> состояться
  12 -> 12
  марта -> март
  телефонного -> телефонный
```

We also created a cell of code to randomly pick five control words from the corpus to serve as examples of how words unrelated to our research question appear in the corpus.

```
import random
from collections import Counter

# Count all lemmatized nouns in the corpus
noun_counts = Counter(
    token.lemma_.lower() for doc in documents_nlp for token in doc if token.pos_ == "NOUN"
)

# Filter lemmatized nouns with at least 4 characters and occurring at least 1000 times
filtered_nouns = [noun for noun, count in noun_counts.items() if len(noun) >= 4 and count >= 1000]

# Randomly select 5 lemmatized nouns from the filtered list
random_nouns = random.sample(filtered_nouns, 5)

print(random_nouns)
```

Initially, we used the matplotlib library to visualize the results of our word count and verify the credibility of our code. However, given that we wanted to produce more visually appealing results, the final code skips this step. Instead, we perform the word count lookup of our lemmatized candidate nouns (to avoid discrepancy between the candidate word and the corresponding lemma), index these on a per-day basis (giving us the sum of candidate word counts per day), and then export these to a .csv file.

```
  import pandas as pd
  
  # --- PARAMETERS: your list of lemmas (all lowercase) ---
  target_lemmas = [nlp(word)[0].lemma_.lower() for word in ['денацификация','демилитаризация', 'бандеровцы', 'англосаксы', 'биолаборатории',\
  'русофобия', 'прокси', 'соотечественников', 'геноцид', 'нацбаты', 'укронацисты',\
  'марионетка', 'референдум', 'освободители', 'бандеризация', 'дезинформация',\
  'воссоединение', 'империалистический','деколонизация', 'спецоперация', 'СВО', 'война',\
  'терроризм', 'вторжение', 'нападение', 'защита',\
  'эффективность', 'частность', 'запрещение', 'размещение', 'проявление']]
  records = []
  
  for row_index, (nlpdoc, date) in enumerate(zip(documents_nlp, df['date'])):

  # 1) total "words" = count of non-punct, non-space tokens
  total_words = sum(1 for tok in nlpdoc if not (tok.is_punct or tok.is_space))
  
  # 2) count each target lemma in this doc
  counts = {  f'Count of {lemma}': sum(1 for tok in nlpdoc if tok.lemma_.lower() == lemma)
  for lemma in target_lemmas }
  
  # 3) assemble the record
  rec = {  'Date': pd.to_datetime(date).date(),
  'Total words': total_words,
  **counts }
  records.append(rec)
  
  # 4) turn into DataFrame and sum up by Date
  daily = (  pd.DataFrame(records)
  .groupby('Date', as_index=False)
  .sum() )
  
  # 5) export
  daily.to_csv('daily_lemma_counts.csv', index=False)
  print(daily.head())
```
  
## Our Results
The following section goes through the results produced through this pipeline. The finished code can count occurences for any set of keywords, but for reasons of space and time we will only consider a small selection here. The primary goal of this section is to demonstrate that our methodology yielded reasonable results, and that they do add empirical clarity to our research question. Therefore, we have chosen not to reproduce the source tables for these visualisations, directing the reader instead to the code above which will reproduce them if required.

### Semi-random control words
![control](https://github.com/hansiverts/SMA-Russian-News-/blob/main/Control%20words%202013-2023.png)
_Green = 'effectiveness' / Blue = 'particularity' / Brown = 'prohibition' / Purple = 'placement' / Yellow = 'appearance'_

This illustration shows our selected control words, giving us an idea of what patterns are produced by typical words in this datset. The control words depicted here are not considered politically loaded, and are not known to be subject to manipulation by the Russian government. Consider this the baseline against which politically sensitive keywords should be compared.


### 'Disinformation'
![disinformation](https://github.com/hansiverts/SMA-Russian-News-/blob/main/Disinformation%202013-2023.png)

This word is not inherently tied to the war. It can be observed that a major spike in its occurrence happens in 2020, which is probably related to the Covid-19 pandemic and disinformation related to the new virus. Another spike occurred in 2021, possibly related to the ongoing vaccine rollout. The word peaks in popularity in early 2022, at the start of the invasion of Ukraine. In the days preceding the 24th of February, the Russian authorities consistently denied its plans to invade the country, dismissing it as disinformation. Moreover, Russia’s crackdown on freedom of speech post-invasion through the law on 'fakes' and 'discreditation' of the army, could also serve as an explanation for the high frequency – through this legislation, the Kremlin accused anti-war and anti-regime voices of spreading 'fake news', effectively weaponizing what it disagreed with.

### 'Referendum'
![referendum](https://github.com/hansiverts/SMA-Russian-News-/blob/main/Referendum%202013-2023.png)

This word is selected to highlight how Russia's narrative has changed since 2014, when a sham referendum was held on Crimea to legitimise its annexation by Russia. It was used almost 50 times per month in 2014, which is when the annexation of Crimea and unlawful referendum on joining the Russian Federation took place. Furthermore, there is an increased use of the word in 2016, possibly tied to the Catalonian independence referendum. In late 2022, 'referendum' appeared almost 40 times per month in the corpus, which is tied to the illegal referendums conducted in Donetsk, Luhansk, Zaporizhia, and Kherson (occupied territories of Ukraine) in September of 2022.

### 'Russophobia' and 'Genocide'
![russophobia and genocide](https://github.com/hansiverts/SMA-Russian-News-/blob/main/Rusophobia%20and%20Genocide%202013-2023.png)
_Purple = 'russophobia' / Green = 'genocide'_

These words appear well before the war, indicating that the narrative portraying the West and Ukraine as Russophobic, as well as claiming that Ukraine is committing genocide on Russian speakers was being built up several years prior. While these elements of the state narrative intensified in 2022 and were used to justify the full-scale invasion, this discourse was present several years before.


### War-related words
![war-related words](https://github.com/hansiverts/SMA-Russian-News-/blob/main/General%20war%20terms%202013-2023.png)
_Orange = 'spetsoperatsiya' / Gold = 'war' / Black = 'invasion' / Red = 'attack' / Blue = 'defence'_

Here, we can see how widely used and relatively neutral words are used in this dataset. The general trend is noisy and unclear, though with some changes near the 2014-2015 low-level conflict in the Donbas, and the full-scale escalation seen in 2022. This noise might, however, show some general trends: For example, the Russian government consistently refuses to recognise its current military campaign as a 'war' as such, opting instead for euphemisms such as 'special military operation'. We searched for 'spetsoperatsiya', a shorthand version, because our algorithm struggles with three-part lemmas. The short version is not used much in official contexts, however, and we can not see a clear visual pattern. Consider also that all these words have other legitimate uses, say, in the Gaza-Israel War.



### 'Denazification' and 'Demilitarisation'
![denazification and demilitarisation](https://github.com/hansiverts/SMA-Russian-News-/blob/main/Denazification%20and%20Demilitarisation%202013-2023.png)
_Red = 'denazification' / Orange = 'demilitarisation'_

'Denazification' and 'demilitarization' were words used by Vladimir Putin in his speech on the morning of the 24th of February 2022, to justify the invasion of Ukraine. Whilst the second was used (albeit rarely) prior to 2022, 'denazification' – a neologism – was never used before. The obvious rise and fall in popularity for these words showcase how they were directly tied to the beginning of the full-scale war. Their subsequent drop indicates that the state quickly shifted away from this particular propaganda narrative. Answering _why this was the case_ would require a different methodological approach and more time.



### Discussion
The goal of this project has been to demonstrate how a quantitative approach like frequency analysis can be employed when studying Russian propaganda. In that, we believe our experiment has been successful. Our findings do give some empirical backing to our hypothesis, namely, that Russia carefully and continually changes its language to justify its military campaign in Ukraine. In our dataset, words like ‘denazification’ and ‘demilitarisation’ appear to follow patterns very different from our random controls, and dual-use terms like ‘genocide’ and ‘referendum’ seem to peak in popularity around politically significant events. The dataset’s uneven size for each month of observations does not appear to correlate with keyword occurrences, as the grey line in each diagram intends to illustrate. Though superficial and narrow by design, our first attempt to measure political phenomena outright (rather than identify, characterise, or understand them only qualitatively) has been highly informative for us. 

The greatest strength of word frequency analysis, when applied to official Russian news outlets, is its relative simplicity. We can immediately see how keyword occurrence rises and falls over time, and whether a word is used ‘naturally’ or ‘artificially’ can often be seen visually without resorting to advanced statistical tools. In this project, we saw that certain keywords – often those with both neutral and politically loaded uses – appeared significantly more often as the 2022 invasion of Ukraine drew nearer. Other keywords appear to have been invented exclusively to shape public opinion – see how ‘denazification’, for instance, suddenly and explosively entered the official vocabulary in February 2022. The 10-year window and near-daily datapoints in our dataset could allow for considerably more detailed analysis – given sufficient word occurrences, rigorous statistical analysis, and enough time. Unlike other techniques covered in the course, however, simple frequency analysis does not require these things to add at least some interpretive value.

The clearest weakness in our project, as we see it, is our method for choosing the keywords themselves. So far, our choices have simply been informed guesses – terms and concepts we knew were politically sensitive, and which we suspected would produce unusual or interesting occurrence patterns over time. Given the nature of our research question, however, we are unsure whether this selection process can be improved much; words employed in propaganda are used non-interchangeably with specifically crafted meanings, after all. Secondly, we cannot control for context with simple word counts. Terms like ‘disinformation’, for example, may be used legitimately to refer to situations unrelated to the Ukraine War. Finally, the dataset only contains articles from the Ministry of Foreign Affairs of Russia. Though a clear and obvious source for government communications, we should not assume they give us a complete picture. Unofficial channels on social media such as Telegram – military generals near the front, for instance – may propagate other narratives (and keywords) not suitable for delivery through ‘verified’ sources.

In summary, we have indicated that Russian narratives are carefully calibrated from official sources, and that these narratives evolve alongside military and political developments. This communication strategy is part of a wider campaign of repression and manipulation at home – and operations in the informational domain abroad. We have shown that even basic word frequency analysis can add to our understanding of such campaigns – more than what a qualitative account would achieve. That said, we also realise that the subject matter is fiendishly complex, and that complete interpretation of our findings is very challenging. Why do keywords disappear after their peaks, and what patterns do they follow? What would the same keywords across different platforms look like? Would word embedding help us differentiate between ‘neutral’ and ‘loaded’ instances of keywords with many uses? If nothing else, the process has taught us a lot about the tools and techniques available to us as researchers, and has made us think a lot about how a similar methodology might be used in more ambitious projects in the future.



### Additional references

Alieva, I., Kloo, I., & Carley, K. M. (2024). ‘Analyzing Russia’s propaganda tactics on Twitter using mixed methods network analysis and natural language processing: a case study of the 2022 invasion of Ukraine’, EPJ Data Science, 13(1).

Lasswell, H. D. (1927). The theory of political propaganda. American Political Science Review, 21, 627–631. https://doi.org/10.2307/1945515

Lai, C., Toriumi, F. and Yoshida, M. (2024) ‘A multilingual analysis of pro Russian misinformation on Twitter during the Russian invasion of Ukraine’, Nature, 14(10155). Available at: https://www.nature.com/articles/s41598-024-60653-y.

Miskimmon, A. and O’Loughlin, B. (2017). ‘Russia’s Narratives of Global Order: Great Power Legacies in a Polycentric World’, Politics and Governance, 5(3), pp. 111–120.


