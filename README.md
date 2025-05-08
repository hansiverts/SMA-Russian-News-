# Justifying War: Word Frequency Analysis and Russia's Changing Narrative on Ukraine
_Social Media Analysis / Group Project Submission / Thursday 8 May 2025_

  _Contributors: Tom-Erik Liljeroth FOSEID, Barbara KUZA-TARKOWSKA, Hans Iver Traaseth SKOGVANG, Mariia VAKULIK_

## Introduction

Russia is famous for its hybrid operations, particularly in the informational space. The country targets audiences inside and outside its borders to manipulate public opinion, and to strengthen support for its government's actions. These narratives are often described as 'propaganda', as they serve to shape the public perception of government policy. In this project, we have looked at a specific kind of propaganda, namely, that which seeks to justify Russia's invasion of Ukraine in 2022 and its ongoing occupation of Eastern Ukrainian regions.

Following the occupation and annexation of Crimea, Russia's rhetoric started to shift. The tone of communication with foreign partners evolved, growing more assertive and more overtly revisionist. Following the Ukraine War's outbreak in 2022, a number of false claims have been developed, distributed, and propagated to justify Russia's invastion of its neighbour (Seskuria, 2022):

- Ukraine is not a sovereign state, but part of a greater Russian civilisation.
- Ukraine is ruled by Nazis, and must be 'denazified' and 'demilitarised'.
- Ukraine commits genocide against its Russian speakers.

The aim of this project is to explore the reach of Russian propaganda quantitatively, through the official communications of the Ministry of Foreign Affairs (MFA). In this experiment, we will attempt to build on pre-existing knowledge by _measuring_ these political phenomena, rather than simply characterising them.


## Political background
Russia’s information warfare doesn’t rely on improvisation. It’s an engineered system of linguistic manipulation aimed at shaping perception as much as battlefield outcomes. It’s a core function of governance, a system where language is policy, and narrative control is as strategic as territorial control. This is not just about censorship or spin; it’s about constructing an internally coherent version of reality, calibrated to insulate the public from the contradictions of government. In this model, propaganda isn't a tool of last resort, but a primary instrument of statecraft. It blurs war and peace, domestic and foreign, fiction and fact, so that the Kremlin can act freely, whilst society remains oriented around a logic that only exists within its carefully managed echo chamber.

Importantly, 'disinformation', a term now global in its use, was already prominent in Russia’s internal discourse due to COVID-19 and international backlash. Ironically, it became a shield for the Kremlin itself, as Russian officials pre-emptively dismissed warnings about their own war plans as 'Western disinformation'. Propaganda, in this case, is not just noise. It’s a methodical attempt to construct an alternate reality in which Russia is always the victim, always justified, and always at war, not just with Ukraine, but with Western Europe and the United States. Our project reveals that the Kremlin's narrative is not chaotic, but deliberate - reactive to global and domestic events.


## The Dataset
The choice of dataset was driven by our intent to understand the development of Russian official rhetoric. The corpus of Russian-language MFA news items covers the period from January 2, 2003, to December 31, 2023, with a total of 56,203 articles. 

 # 5) export
 daily.to_csv('daily_lemma_counts.csv', index=False)
 
 print(daily.head())
