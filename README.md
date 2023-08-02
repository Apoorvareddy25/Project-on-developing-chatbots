import random
import re
import nltk
from nltk.tokenize import word_tokenize
from nltk.corpus import stopwords
from nltk.stem import WordNetLemmatizer

# Download NLTK resources (only needed once)
nltk.download("punkt")
nltk.download("stopwords")
nltk.download("wordnet")

# Define the chatbot responses
responses = {
    "hello": ["Hello!", "Hi there!", "Hey!", "Nice to meet you!"],
    "how are you": ["I'm good, thank you!", "I'm doing well.", "All good!"],
    "what is your name": ["My name is Chatbot.", "I am called Chatbot.", "You can call me Chatbot."],
    "bye": ["Goodbye!", "See you later!", "Bye! Have a great day!"],
    "tell me a joke": [
        "Why don't scientists trust atoms? Because they make up everything!",
        "I told my wife she was drawing her eyebrows too high. She seemed surprised.",
        "Parallel lines have so much in common. It's a shame they'll never meet.",
    ],
    "what can you do": ["I can have a conversation with you and tell you jokes.", "I can answer general questions.",],
    "default": ["I'm sorry, I don't understand.", "Could you please rephrase that?", "I'm still learning."],
}

# Function to preprocess user input
def preprocess_input(user_input):
    lemmatizer = WordNetLemmatizer()
    stop_words = set(stopwords.words("english"))
    words = word_tokenize(user_input.lower())
    words = [lemmatizer.lemmatize(word) for word in words if word.isalnum()]
    words = [word for word in words if word not in stop_words]
    return " ".join(words)

# Function to calculate the similarity between two sentences using Jaccard similarity
def jaccard_similarity(sentence1, sentence2):
    words_set1 = set(sentence1.split())
    words_set2 = set(sentence2.split())
    intersection = len(words_set1.intersection(words_set2))
    union = len(words_set1.union(words_set2))
    return intersection / union if union != 0 else 0

# Function to select a response based on similarity with user input
def get_response(user_input):
    user_input = preprocess_input(user_input)
    max_similarity = 0
    selected_response = responses["default"][0]
    for key in responses.keys():
        similarity = jaccard_similarity(user_input, key)
        if similarity > max_similarity:
            max_similarity = similarity
            selected_response = random.choice(responses[key])
    return selected_response

# Main function to interact with the chatbot
def main():
    print("Chatbot: Hi! I'm your chatbot. Ask me anything or say 'bye' to exit.")
    while True:
        user_input = input("You: ")
        if user_input.lower() == "bye":
            print("Chatbot: Goodbye! Have a great day!")
            break
        response = get_response(user_input)
        print("Chatbot:", response)

if __name__ == "__main__":
    main()
