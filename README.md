## Introduction:
Dogs are one of the most popular domesticated animals, and there are hundreds of different breeds. Each breed has unique characteristics, including size, lifespan, litter size, speed, intelligence, and suitability for certain lifestyles or activities. Understanding the relationships between these characteristics can be valuable for breeders, pet owners, and researchers.



## Aim:
The aim of this analysis is to explore the correlations between different characteristics of dog breeds. This includes investigating the relationship between weight and height, litter size, lifespan, and average height across different breed groups. Additionally, we will examine the suitability of different breeds for certain lifestyles or activities, as well as identifying hypoallergenic breeds.
## Ask:
Here we asked the following question:
- What is the correlation between weight, height, and lifespan among dog breeds?
- How does litter size vary across different dog breeds?
- Which breed is considered the fastest and slowest?
- What are some hypoallergenic breeds, and what does hypoallergenic mean?
- How many breeds are there in the Foundation Stock Service group?
- How does lifespan vary across different breed groups?
- What are the common characteristics of intelligent and affectionate breeds?
- Is there a correlation between weight and litter size among dog breeds?
- Which breed group has the shortest and tallest dogs?
- Which breeds are suitable for specific lifestyles or activities?

## Analysis
```ruby
import pandas as pd
import numpy as np
import seaborn as sns
import matplotlib 
from matplotlib import pyplot as plt
from matplotlib import style
plt.style.use('ggplot')

dog_breeds = pd.read_csv("BREEDS.csv", encoding="ISO-8859-1")
print(dog_breeds.describe())

# calculate correlation between weight and height
correlation_weight_height = dog_breeds["Max_weight"].corr(dog_breeds["Max_height"])

# calculate correlation between weight and lifespan
correlation_weight_lifespan = dog_breeds["Max_weight"].corr(dog_breeds["Max_life_span"])

print("Correlation between weight and height:", correlation_weight_height)
print("Correlation between weight and lifespan:", correlation_weight_lifespan)

# plot a scatter plot to visualize the correlation between weight and height
sns.scatterplot(x="Max_weight", y="Max_height", data=dog_breeds)
sns.scatterplot(x="Max_weight", y="Max_life_span", data=dog_breeds)

grouped_Max_litter_size = dog_breeds.groupby("Breed_Name")["Max_litter_size"].sum().sort_values(ascending = 0).head(5)
grouped_Min_litter_size = dog_breeds.groupby("Breed_Name")["Max_litter_size"].sum().sort_values(ascending = 0).tail(5)
print(grouped_Max_litter_size)
print(grouped_Min_litter_size)



# calculate average speed of each breed
dog_breeds["Avg_speed"] = dog_breeds["Speed_value"].mean()

# identify the fastest and slowest breeds
fastest_breed = dog_breeds.loc[dog_breeds["Speed_value"].idxmax()]["Breed_Name"]
slowest_breed = dog_breeds.loc[dog_breeds["Speed_value"].idxmin()]["Breed_Name"]
print("Fastest dog breed is", fastest_breed)
print("Slowest dog breed is", slowest_breed)

#  breeds that are Hypoallergenic
hypoallergenic_breeds = dog_breeds.loc[dog_breeds['Hypoallergenic'] == 'Yes', 'Breed_Name'].tolist()
print('HYPOALLERGENIC DOG BREEDS:', 
      hypoallergenic_breeds)

# top 5 dog breeds origin and the number of breeds from each origin
grouped_Breed_Groups = dog_breeds.groupby("Breed_Group")["Breed_Name"].nunique().sort_values(ascending = 0)
print(grouped_Breed_Groups)
grouped_Breed_Groups.plot(kind="bar", y ="Breed_Group", color=["black"])

# Create an empty dictionary to store the frequency counts of each word
word_counts = {}

# Loop through each breed description and split the text into individual words
for desc in dog_breeds['Temperament']:
    for word in desc.split():
        # Remove any punctuation or special characters from the word
        word = word.strip('.,;:"\'!?()[]{}')
        # Convert the word to lowercase to ensure consistency in counts
        word = word.lower()
        # Increment the frequency count for the word
        if word in word_counts:
            word_counts[word] += 1
        else:
            word_counts[word] = 1

# Sort the word frequency counts in descending order
sorted_counts = sorted(word_counts.items(), key=lambda x: x[1], reverse=True)


# Create a list of keywords to search for in the breed descriptions
keywords = ['intelligent', 'loyal', 'friendly', 'affectionate', 'protective', 'energetic', 'independent']

# Create a dictionary to store the frequency counts of each keyword
keyword_counts = {k: 0 for k in keywords}

# Loop through each breed and search for the keywords in the breed description
for i, breed in enumerate(dog_breeds['Breed_Name']):
    desc = dog_breeds.loc[i, 'Temperament']
    for keyword in keywords:
        if keyword in desc.lower():
            keyword_counts[keyword] += 1
    # Print the breed name and identified traits
    traits = [k for k, v in keyword_counts.items() if v > 0]
    print(f"{breed}: {traits}")

    # Reset the keyword counts for the next breed
    keyword_counts = {k: 0 for k in keywords}
    
# Calculate the average lifespan for each breed group
group_lifespans = dog_breeds.groupby("Breed_Group")["Max_life_span"].mean()
group_lifespans
# Print the breed group with the longest and shortest average lifespan
longest_lifespan_group = group_lifespans.idxmax()
shortest_lifespan_group  = group_lifespans.idxmin()
print ("The group with the longest average lifespan:", longest_lifespan_group)
print("The group with the shortest average lifespan:", shortest_lifespan_group)

from collections import Counter

breed_temp = " ".join(list(dog_breeds["Temperament"]))

# Removing punctuations and converting to lowercase
breed_temp = ''.join(e.lower() for e in breed_temp if e.isalnum() or e.isspace())

# Tokenizing the Temperament
tokens = breed_temp.split()

# Finding the most common words
common_words = Counter(tokens).most_common(10)

print("The 10 most common words in the breed Temperament are:")
for word, count in common_words:
    print(f"{word}: {count} times")


breeds_by_temperament = dog_breeds.groupby('Temperament')['Breed_Name'].apply(list).head(30)
breeds_by_temperament


correlation_weight_lifesize = dog_breeds["Max_weight"].corr(dog_breeds["Max_litter_size"])
print("Correlation between weight and lifesize:", correlation_weight_lifesize)
# Creating a scatter plot of weight versus litter size
sns.scatterplot(x="Max_weight", y="Max_litter_size", data=dog_breeds)


grouped = dog_breeds.groupby('Breed_Group')['Min_height', 'Max_height',].mean()
print(grouped)
grouped.plot(kind="bar", figsize=(7,5), color=["black", "blue",])

plt.title("Average Dog Weight and Height by Hypoallergenic Status")
plt.xlabel("Breed_Group")
plt.ylabel("Average Weight/Height")
plt.show()


suitable_for_children = dog_breeds.loc[dog_breeds['Suitable_For'].str.contains('children', case=False), 'Breed_Name'].tolist()
suitable_for_apartment = dog_breeds.loc[dog_breeds['Suitable_For'].str.contains('apartment', case=False), 'Breed_Name'].tolist()
children_breeds = dog_breeds['Suitable_For'].str.contains('children', case=False).sum()
apartment_breeds = dog_breeds['Suitable_For'].str.contains('apartment', case=False).sum()
families_breeds = dog_breeds['Suitable_For'].str.contains('families', case=False).sum()
print('Number of Dog Breeds Suitable for Children:', children_breeds)
print('Number of Dog Breeds Suitable for apartment:', apartment_breeds)
print("BREEDS SUITABLE FOR APARTMENT:", suitable_for_apartment)
print("BREEDS SUITABLE FOR CHILDREN:", suitable_for_children)


# filter breeds by size
small_breeds = dog_breeds[(dog_breeds["Max_weight"] <= 25) & (dog_breeds["Max_height"] <= 18)]
medium_breeds = dog_breeds[(dog_breeds["Max_weight"] <= 50) & (dog_breeds["Max_height"] <= 24)]
large_breeds = dog_breeds[(dog_breeds["Max_weight"] > 50) & (dog_breeds["Max_height"] > 24)]

# print the recommended breeds based on size
print("Recommended small breeds:")
print(small_breeds["Breed_Name"].tolist()[:10])
print("Recommended medium breeds:")
print(medium_breeds["Breed_Name"].tolist()[:10])
print("Recommended large breeds:")
print(large_breeds["Breed_Name"].tolist()[:10])


# Filter for hypoallergenic breeds
hypoallergenic_breeds = dog_breeds[dog_breeds["Hypoallergenic"] == "Yes"]


# Identify common temperaments among hypoallergenic breeds
temperaments = hypoallergenic_breeds["Temperament"].str.split(", ")
common_temperaments = pd.Series([t for sublist in temperaments for t in sublist]).value_counts().sort_values(ascending = 0).head(10)
common_temperaments.plot(kind="bar", y ="Temperament", color=["black"])


Top_5_dog_Breed_Origin = dog_breeds.groupby("Breed_Origin")["Breed_Name"].nunique().sort_values(ascending = 0).head(10)
print(Top_5_dog_Breed_Origin)
```


## Dashboard
https://user-images.githubusercontent.com/106782819/229256811-14cb2957-6edb-46f7-81e1-f4d296d37947.mp4
### Link to Dashboard:
https://app.powerbi.com/links/FR9Wyaq_lO?ctid=140ab389-b14b-4153-b9ab-3b333b7a78b3&pbi_source=linkShare&bookmarkGuid=45f2daff-6187-4fb4-8c2f-f5cd6ccade9a




## Findings:
- There is a positive correlation between weight and height among dog breeds, but not a strong correlation between weight and lifespan.
- Small dog breeds such as the Chihuahua and Affenpinscher tend to have smaller litters, while larger breeds such as the American Bulldog and Catahoula Leopard Dog tend to have larger litters.
- The Greyhound breed is the fastest, while the Pekingese breed is one of the slowest.
- Some hypoallergenic breeds include the Black Russian Terrier, Borzoi, and Barbet, but hypoallergenic does not necessarily mean completely allergy-free.
- The Foundation Stock Service group has 68 dog breeds.
- Smaller breed groups such as the Toy group tend to have longer lifespans, while larger breed groups such as the Working group tend to have shorter lifespans.
- Breeds such as the Brittany and Miniature American Shepherd are intelligent, active, and friendly, while breeds such as the Tibetan Spaniel and Border Terrier are affectionate, lively, and alert.
- There is a slight positive correlation between weight and litter size among dog breeds, with larger breeds tending to have slightly larger litters.
- Toy breed groups are the shortest, with an average height of around 9-11 inches, while the Working groups are the tallest, with an average height of around 23-28 inches.
- Certain breeds are suitable for specific lifestyles or activities, such as the German Pinscher and Bearded Collie for families with children and the Chihuahua and Border Terrier for apartment living due to their small size and lower exercise requirements.

## Recommendations:
Breeds like the Chihuahua and Border Terrier may be more suitable for individuals living in smaller spaces, due to their smaller size and lower exercise requirements.
For individuals with children, breeds like the German Pinscher and Bearded Collie may be more suitable due to their friendly and active nature.
Individuals with allergies, consider adopting breeds like the Black Russian Terrier, Borzoi, and Barbet, which are known for being hypoallergenic.
If individuals are seeking a high-energy companion, breeds like the Brittany and Miniature American Shepherd may be more suitable due to their intelligence and active nature.
For individuals seeking a more laid-back companion, breeds like the Tibetan Spaniel and Border Terrier may be more suitable


