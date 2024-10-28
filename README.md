# Book-recommendation-system
A simple program, that recommends books for users based on their nearest neighbour by calculating he manhattan distances between them. The smaller distance indicates the nearest neighbour
The system uses user-book ratings to make recommendations for other users.

## Table of Contents
- [Overview](#overview)
- [Features](#features)
- [Installation](#installation)
- [Usage](#usage)
- [Data](#data)
- [Implementation](#implementation)
  - [User Ratings](#user-ratings)
  - [Manhattan Distance Function](#manhattan-distance-function)
  - [Similarity Matrix](#similarity-matrix)
  - [Finding Recommendations](#finding-recommendations)
- [Future Enhancements](#future-enhancements)
- [License](#license)

## Overview
The User-Based Book Recommendation System identifies the most similar user (nearest neighbor) based on Manhattan distance between their ratings, and then recommends books that the nearest neighbor has rated but the target user has not.

## Features
- Compute Manhattan distance between user ratings.
- Identify the nearest neighbor for a given user.
- Recommend books based on nearest neighbor's ratings.
- Display similarity matrix and recommendations in a tabular format.

## Installation
1. **Clone the repository**:
   ```sh
   git clone https://github.com/yourusername/Book-recommendation-system.git
2. **Navigate to the project directory:**
   ```sh
   cd Book-recommendation-system
3. **Install required dependencies:**
   ```sh
   pip install pandas tabulate
  ## Usage
1. **Run the script:**
   ```sh
   python Recommendation.py
2. **Interact with the chatbot:**
   Follow the prompts to enter your queries and get book recommendations.

## Data
The project uses a sample dataset of user-book ratings stored in a dictionary. Each user has rated a different set 
of books on a scale of 1 to 10. Here is the 
sample dataset used in this project:
ReaderBookRatings = {
    'Abbey': {'The Exorcist': 10, 'Dune': 5, 'The Hobbit': 9, 'Black Beauty': 6},
    'Bob': {'Odyssey': 8, 'Beloved': 3, 'Dune': 10, 'The Hobbit': 5, 'Black Beauty': 7},
    'Cody': {'Hamlet': 7, 'Trust': 4, 'The Exorcist': 5, 'Dune': 9, 'The Hobbit': 1},
    'Dan': {'Beloved': 5, 'Dune': 7, 'The Hobbit': 4},
    'Emily': {'Odyssey': 3, 'Beloved': 8, 'Dune': 1, 'Black Beauty': 7},
    'Fausa': {'Trust': 3, 'The Exorcist': 10, 'Dune': 6, 'The Hobbit': 10}
}

## Implementation
### User Ratings
Define the user-book ratings in a dictionary:

### Manhattan Distance Function
Define the function to calculate the Manhattan distance:

def manhattanD(ratings1D, ratings2D):
    distance = 0
    for book in ratings1D.keys():
        if book in ratings2D.keys():
            x = ratings1D[book]
            y = ratings2D[book]
            distance += abs(x - y)
    return distance

### Similarity Matrix
Calculate the Manhattan distances between all pairs of users and create a similarity matrix:

manhattan_distances = {}
for ReaderX in ReaderBookRatings:
    manhattan_distances[ReaderX] = {}
    for ReaderY in ReaderBookRatings:
        if ReaderX != ReaderY:
            manhattan_distances[ReaderX][ReaderY] = manhattanD(ReaderBookRatings[ReaderX], ReaderBookRatings[ReaderY])
        else:
            manhattan_distances[ReaderX][ReaderY] = '-'

similarity_matrix = pd.DataFrame(manhattan_distances)
print('\nSimilarity Matrix:')
print(tabulate(similarity_matrix, headers='keys', tablefmt='fancy_grid'))
**Similarity matrix output:**
╒═══════╤═════════╤═══════╤════════╤═══════╤═════════╤═════════╕
│       │ Abbey   │ Bob   │ Cody   │ Dan   │ Emily   │ Fausa   │
╞═══════╪═════════╪═══════╪════════╪═══════╪═════════╪═════════╡
│ Abbey │ -       │ 10.0  │ 17.0   │ 7.0   │ 5.0     │ 2.0     │
├───────┼─────────┼───────┼────────┼───────┼─────────┼─────────┤
│ Bob   │ 10.0    │ -     │ 5.0    │ 6.0   │ 19.0    │ 9.0     │
├───────┼─────────┼───────┼────────┼───────┼─────────┼─────────┤
│ Cody  │ 17.0    │ 5.0   │ -      │ 5.0   │ 8.0     │ 18.0    │
├───────┼─────────┼───────┼────────┼───────┼─────────┼─────────┤
│ Dan   │ 7.0     │ 6.0   │ 5.0    │ -     │ 9.0     │ 7.0     │
├───────┼─────────┼───────┼────────┼───────┼─────────┼─────────┤
│ Emily │ 5.0     │ 19.0  │ 8.0    │ 9.0   │ -       │ 5.0     │
├───────┼─────────┼───────┼────────┼───────┼─────────┼─────────┤
│ Fausa │ 2.0     │ 9.0   │ 18.0   │ 7.0   │ 5.0     │ -       │
╘═══════╧═════════╧═══════╧════════╧═══════╧═════════╧═════════╛

### Finding Recommendations
Identify the nearest neighbor and recommend books:

recommendations = []
for ReaderX in ReaderBookRatings:
    min_distance = float('inf')
    nearest_neighbour = None
    for ReaderY in ReaderBookRatings:
        if ReaderX != ReaderY:
            distance = manhattan_distances[ReaderX][ReaderY]
            if distance != '-' and distance < min_distance:
                min_distance = distance
                nearest_neighbour = ReaderY
    recommended_books = []
    for book, rating in ReaderBookRatings[nearest_neighbour].items():
        if book not in ReaderBookRatings[ReaderX]:
            recommended_books.append((book, rating))
    recommendations.append({'Reader': ReaderX, 'Recommended Books': recommended_books})

recommendations_table = pd.DataFrame(recommendations)
print('\nRecommendations:')
print(tabulate(recommendations_table, headers='keys', showindex=False, tablefmt='fancy_grid'))
**Recommendations output:**
╒══════════╤═══════════════════════════════════════════════════════╕
│ Reader   │ Recommended Books                                     │
╞══════════╪═══════════════════════════════════════════════════════╡
│ Abbey    │ [('Trust', 3)]                                        │
├──────────┼───────────────────────────────────────────────────────┤
│ Bob      │ [('Hamlet', 7), ('Trust', 4), ('The Exorcist', 5)]    │
├──────────┼───────────────────────────────────────────────────────┤
│ Cody     │ [('Odyssey', 8), ('Beloved', 3), ('Black Beauty', 7)] │
├──────────┼───────────────────────────────────────────────────────┤
│ Dan      │ [('Hamlet', 7), ('Trust', 4), ('The Exorcist', 5)]    │
├──────────┼───────────────────────────────────────────────────────┤
│ Emily    │ [('The Exorcist', 10), ('The Hobbit', 9)]             │
├──────────┼───────────────────────────────────────────────────────┤
│ Fausa    │ [('Black Beauty', 6)]                                 │
╘══════════╧═══════════════════════════════════════════════════════╛

## Future Enhancements
- **Natural Language Processing:** Implement NLP to handle a wider range of queries.
- **Data Visualization:** Integrate data visualization for more insightful responses.
- **Web Interface:** Develop a web interface for easier interaction with the recommendation system.
