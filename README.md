# Justifying War: Word Frequency Analysis and Russia's Changing Narrative on Ukraine
_Social Media Analysis / Group Project Submission / Thursday 8 May 2025_

  _Contributors: Tom-Erik Liljeroth FOSEID, Barbara KUZA-TARKOWSKA, Hans Iver Traaseth SKOGVANG, Mariia VAKULIK_



## Introduction

Russia is famous for its hybrid operations, particularly in the informational space. The country targets audiences inside and outside its borders to manipulate public opinion, and to strengthen support for its government's actions. These narratives are often described as 'propaganda', as they serve to shape the public perception of government policy. In this project, we have looked at a specific kind of propaganda, namely, that which seeks to justify Russia's invasion of Ukraine in 2022 and its ongoing occupation of Eastern Ukrainian regions.

Following the occupation and annexation of Crimea, Russia's rhetoric started to shift. The tone of communication with foreign partners evolved, growing more assertive and more overtly revisionist. Following the Ukraine War's outbreak in 2022, a number of false claims have been developed, distributed, and propagated to justify Russia's invastion of its neighbour (Seskuria, 2022):

- Ukraine is not a sovereign state, but part of a greater Russian civilisation.
- Ukraine is ruled by Nazis, and must be 'denazified' and 'demilitarised'.
- Ukraine commits genocide against its Russian speakers.

The aim of this project is to explore the reach of Russian propaganda quantitatively, through the official communications of the Ministry of Foreign Affairs (MFA). In this experiment, we will attempt to build on pre-existing knowledge by _measuring_ these political phenomena, rather than simply characterising them. In essence, we will seek to answer this question:

> How has Russia’s war rationale  evolved during the Ukraine War?



## Political background
Russia’s information warfare doesn’t rely on improvisation. It’s an engineered system of linguistic manipulation aimed at shaping perception as much as battlefield outcomes. It’s a core function of governance, a system where language is policy, and narrative control is as strategic as territorial control. This is not just about censorship or spin; it’s about constructing an internally coherent version of reality, calibrated to insulate the public from the contradictions of government. In this model, propaganda isn't a tool of last resort, but a primary instrument of statecraft. It blurs war and peace, domestic and foreign, fiction and fact, so that the Kremlin can act freely, whilst society remains oriented around a logic that only exists within its carefully managed echo chamber.

Importantly, 'disinformation', a term now global in its use, was already prominent in Russia’s internal discourse due to COVID-19 and international backlash. Ironically, it became a shield for the Kremlin itself, as Russian officials pre-emptively dismissed warnings about their own war plans as 'Western disinformation'. Propaganda, in this case, is not just noise. It’s a methodical attempt to construct an alternate reality in which Russia is always the victim, always justified, and always at war, not just with Ukraine, but with Western Europe and the United States. Our project reveals that the Kremlin's narrative is not chaotic, but deliberate - reactive to global and domestic events.



## The Dataset
The choice of dataset was driven by our intent to understand the development of Russian official rhetoric. While we initally struggled with finding a fitting dataset – initially failing to produce interesting results from a 1000-article Factiva dataset – we eventually discovered a comprehensive dataset of all Russian language news items published on the Russian Ministry of Foreign Affairs webpage. The dataset, retrievable from [this link](https://tadadit.xyz/datasets/2024/russian_institutions_2024/mid.ru_ru_2024/), was compiled by researched Giorgio Comai as part of a research project supported by the Italian Ministry of Foreign Affairs. The full corpus covers the period from January 2, 2003, to December 31, 2023, with a total of 56,203 articles. A corresponding dataset of English language Russian MFA news items is also available, but is significantly smaller and therefore inferior to the Russian language corpus. 



## The Code

The code involved in running Spacy’s Natural Language Processing (NLP) - while simple - has gone through many iterations. Initially, the code processed the entire dataset of 56,203 articles, totalling over 20 million words – a volume of data that led to some computational problems. Simply running the NLP pipeline took multiple hours, even though we used the smallest Russian language pipeline offered in the Spacey library (ru_core_news_md). For every time the Python kernel stopped, this process needed to be restarted, causing the time involved in exploring the dataset and tinkering with the code to draw out significantly. 

To address this, we attempted to use the in-built pickle module that comes with Python. Given that it produced a 14 gigabyte .pkl file that then required a multi-hour process to be unpacked on every run of the Python kernel, the issue was only exacerbated. Moreover, it led to a sequence of errors in the rest of the code, producing yet another headache. In sum, pickling created more issues than it solved.

A more practical solution was reached when we simply decided to trim the dataset temporally, removing data rows from before 2013. For the purposes of our project, we didn’t need the data in the preceding decade. This virtually cut the data volume in half – drastically reducing processing time – allowing us to finally begin exploring the data. In the end, our dataset spans 28,706 individual articles and 14,004,206 words.

In order to establish that the dataset was indeed readable and that the Russian NLP pipeline had successfully lemmatized and tokenized the data, the finished code contains multiple lines designed to verify this. Lemmatizing is a process that is particularly important given the morphology of the Russian language, in which one can find upwards of 30 inflections (variations) of a single word. Faulty lemmatizing could therefore provide a drastically lower count of our candidate words. As demonstrated in the following code output, this was done successfully. 

```
  for token in nlp_doc[:10]:
      print (token,'->',token.lemma_)
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

We also created a cell of code to randomize five candidate words.

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
  
## Findings!
The following section goes through the results produced through this pipeline. The finished code can count occurences for any set of keywords, but for reasons of space and time we will only consider a small selection here. The primary goal of this section is to demonstrate that our methodology yielded reasonable results, and that they do add empirical clarity to our research question. Therefore, we have chosen not to reproduce the source tables for these visualisations, directing the reader instead to the code above which may reproduce them if required.

### Control
![control](https://github.com/hansiverts/SMA-Russian-News-/blob/main/Control%20words%202013-2023.png)
_Green = 'effectiveness' / Blue = 'particularity' / Brown = 'prohibition' / Purple = 'placement' / Yellow = 'appearance'_

This illustration shows selected control words, giving us an idea of what patterns are produced by typical words in this datset. The control words depicted here are not considered politically loaded, and are not known to be subject to manipulation by the Russian government. Consider this the baseline against which politically sensitive keywords should be compared.

---

Finding II: Disinformation

The word “disinformation” is not inherently tied to the war. It can be observed that a major spike in its occurrence happens in 2020, which is probably related to the Covid-19 pandemic and the disinformation around the virus. Another spike occurred in 2021, possibly related to the ongoing vaccine rollout. However, at its peak in early 2022, the word “disinformation” was used over 16 times, in the context of the invasion of Ukraine. In the days preceding the 24th of February, the Russian authorities consistently denied its plans to invade the country, dismissing it as disinformation. Moreover, Russia’s crackdown on freedom of speech post-invasion through the law on “fakes” and “discreditation” of the army, could also serve as an explanation for the high frequency – through the legislation, the Kremlin accused anti-war and anti-regime voices of spreading “fake news”, effectively weaponizing what it viewed as disinformation.

Finding III: Referendum 

The occurrence of the word “referendum” provides insight into Russian propaganda since the beginning of the conflict with Ukraine in 2014. It was used almost 50 times in 2014, which is when the annexation of Crimea and unlawful referendum on joining the Russian Federation took place. Furthermore, there is an increased use of the word in 2016, possibly tied to the Catalonian independence referendum. In late 2022, “referendum” appeared almost 40 times in the corpus, which is tied to the illegal referendums conducted in Donetsk, Luhansk, Zaporizhia, and Kherson (occupied territories of Ukraine) in September of 2022. This showcases that Russian propaganda intensifies its rhetoric of referendums to justify annexations of Ukrainian territory. 

Finding IV: Russophobia and genocide

The word “Russophobia” (in purple) and “genocide” (in green) appear well before the war, indicating that the narrative portraying the West and Ukraine as Russophobic, as well as claiming that Ukraine is committing genocide on Russian speakers was being built up several years prior. This is especially seen with the word “Russophobia” which is used numerous times between 2016 and 2020. “Genocide” occurs less frequently, although there is a small peak in 2015, probably linked to the war in Donbas, which was described by the Kremlin as genocide by Ukrainians on Russian-speakers. Yet, there is a clear spike of both words in 2022, with the occurrence of “Russophobia” reaching 50 at the time of the invasion. The frequency of the word “genocide” also sharply increases, being used almost 40 times. Thus, while these elements of the state narrative intensified in 2022 and were used to justify the full-scale invasion, this discourse was present several years before.


Finding V: War-related terms

The graph below shows the monthly occurrence of the following war-related terms: “special operation”, “war”, “invasion”, “attack”, and “defense”. While the frequency of the word “war” fluctuates in the given time period, two major peaks can be observed in 2022 and in 2023. The high prevalence of “war” in 2022 is surprising, given the Kremlin’s avoidance of naming the invasion as war – in official state discourse it was rather referred to as a “special military operation”. However, a possible explanation could be references to West officials describing the conflict as a war,  or simply references to other global conflicts. This is especially plausible in the context of the 2023  peak – the year of the October 7th attack on Israel and beginning of the war in Gaza, a topic which Russian state officials often draw attention to. The word “special operation” has a scarce occurrence, which is contrary to the research assumptions. Given the excessive use of the term “special military operation” or “SVO” in state media, a higher frequency could be expected. Nevertheless, the counted term is one word, translated as “spec-operation”, rather than the whole term “special military operation” –  a methodological choice which could have influenced the result. All other terms increase at the beginning of 2022, although “attack”, “invasion”, and “special operation” to a lesser extent. There is a clear peak of the word “defense”, occuring over 120 times in 2022, possibly tied to the propaganda narrative of “defending the Russian-speakers on whom Ukraine is committing genocide”. 



Finding VI: “Denazification and demilitarization”

“Denazification” and “demilitarization” were words used by Vladimir Putin in his speech on the morning of the 24th of February 2022, to justify the invasion of Ukraine. While “demilitarisation” (in orange) was scarcely used prior to 2022, “denazification” (in red) – a neologism – was never used before. Both words were used over 70 times since the beginning of 2022, showcasing how they were directly tied to the beginning of the full-scale war. However, there is a rapid drop in the usage of the word from March 7th 2022, indicating that the state quickly shifted away from this particular propaganda narrative. Why this was the case could be the object of a separate study.



Finding VII: Other key terms

The frequency of other terms related to Russian propaganda narratives surrounding the Russian invasion of Ukraine was also counted. This includes: “banderite” and “banderisation” (in blue), “Ukro-Nazis” (in yellow), “Anglo-Saxon” (in red), and “biolaboratories” (in black). A rise in the term “biolaboratories” occurs in March, tied to the narrative of American biolaboratories in Ukraine in which allegedly biological weapons were developed. This narrative served as an element of justification of the invasion. However, the frequency of the term drops from May 2022 onwards. 

When it comes to “banderites” and “banderisation”, the words peak in June and September of 2022, around the time of the referendum in the occupied territories. Thus, the “banderite” narrative probably served as a tool to justify the referendums by the alleged oppression of the population in those areas by the “banderite” Ukrainians. 

There is limited occurrence of the word “Ukro-Nazis”, from which a concrete conclusion cannot be drawn. Moreover, the word “Anglo-Saxon” has several peaks, most notably occurring over 15 times in March of 2023 and over 20 times in June 2023. 
