**Justifying Propaganda**

Test
Headline 1
# Save the target words to a CSV file
import pandas as pd

# --- PARAMETERS: your list of lemmas (all lowercase) ---
target_lemmas = [nlp(word)[0].lemma_.lower() for word in ['денацификация','демилитаризация', 'бандеровцы', 'англосаксы', 'биолаборатории',\
                                                           'русофобия', 'прокси', 'соотечественников', 'геноцид', 'нацбаты', 'укронацисты',\
                                                              'марионетка', 'референдум', 'освободители', 'бандеризация', 'дезинформация',\
                                                                  'воссоединение', 'империалистический','деколонизация', 'спецоперация', 'СВО', 'война',\
                                                                    'терроризм', 'вторжение', 'нападение', 'защита',\
                                                                          'эффективность', 'частность', 'запрещение', 'размещение', 'проявление']]

#target_lemma = nlp("марионетка")[0].lemma_.lower() consider lemmatizing the target words "соотечественников" 

# We'll build one record per row/document
records = []

for row_index, (nlpdoc, date) in enumerate(zip(documents_nlp, df['date'])):
    # 1) total "words" = count of non-punct, non-space tokens
    total_words = sum(1 for tok in nlpdoc if not (tok.is_punct or tok.is_space))
    
    # 2) count each target lemma in this doc
    counts = {
        f'Count of {lemma}': sum(1 for tok in nlpdoc if tok.lemma_.lower() == lemma)
        for lemma in target_lemmas
    }
    
    # 3) assemble the record
    rec = {
        'Date': pd.to_datetime(date).date(),
        'Total words': total_words,
        **counts
    }
    records.append(rec)

# 4) turn into DataFrame and sum up by Date
daily = (
    pd.DataFrame(records)
      .groupby('Date', as_index=False)
      .sum()
)

# 5) export
daily.to_csv('daily_lemma_counts.csv', index=False)

print(daily.head())
