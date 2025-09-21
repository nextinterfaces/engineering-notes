# 🧩 Tokenization, Embeddings & Attention 

## 1. Why Should You Care?  

Imagine you’re trying to explain Shakespeare to a computer 💻.  
You say: *“To be, or not to be.”*  

- Computer: 🤔 “What’s a ‘word’? What’s a ‘sentence’?”  
- We need to break language into **small units (tokens)**.  

👉 Step 1: **Tokenization**  
👉 Step 2: **Embeddings** (turn into math)  
👉 Step 3: **Attention** (focus on important parts)  

---  

## 2. Tokenization: Breaking Down Language  

Think of it like LEGO bricks 🧱:  
- A sentence = LEGO castle  
- Words/pieces = LEGO bricks  

```
Sentence: "Cats love pizza!"
Tokens:   ["Cats", "love", "pizza", "!"]
```  

But sometimes:  
```
"unhappiness" → ["un", "happiness"]
"tokenization" → ["token", "ization"]
```  

👉 Computers don’t see “words”, they see **IDs** (numbers).  

---  

## 3. Embeddings: Turning Words Into Math  

Now we take tokens and place them in **meaning space**.  

```
        (dog) •
              |
        (puppy) •      (kitten) • (cat) 
```  

- Similar meanings = close points.  
- Different meanings = far apart.  

📌 Each word = a **vector of numbers**.  

---  

## 4. Attention: Who’s Talking to Whom?  

Imagine you’re in a noisy classroom 🎓.  
You only pay attention to the teacher, not everyone shouting.  

👉 That’s **attention**: focus on the *relevant tokens*.  

```
Sentence: "The cat chased the mouse because it was hungry."

Who is "it"? 
Attention → focuses on "cat" 🐈 (not mouse 🐭)
```  

---  

## 5. Why Attention is Magical ✨  

- Handles **long sentences**.  
- Captures **relationships** across words.  
- Forms the foundation of **Transformers** (like GPT).  

```
[Input Tokens] ---> [Embeddings] ---> [Attention Layers] ---> [Meaningful Output]
```  

---  

## 6. Quick Game 🎲  

Q: In the sentence *“She poured water in the cup because it was empty”*,  
What does **“it”** refer to?  

👉 With **attention**, model figures out → “the cup” 🥤.  

---  

## 7. Popular Tools  

- **Tokenizers**: HuggingFace, SentencePiece, BPE  
- **Embeddings**: OpenAI, Cohere, HuggingFace Transformers  
- **Attention Models**: BERT, GPT, T5  

---  

## 8. Exercise 💪  

Take this sentence:  
*“I love my dog because he is loyal.”*  

- Tokens = ?  
- Embeddings = ? (concept of loyalty near dog)  
- Attention = focuses on “dog” when reading “he”.  

👉 Try drawing it in your notebook!  

---  

## 9. The Big Picture  

```
[Text]
   |
   v
 Tokenization (split into pieces)
   |
   v
 Embeddings (numbers in vector space)
   |
   v
 Attention (find important relationships)
   |
   v
 Understanding / Generation 🚀
```  

---  

## 10. Recap  

- Tokenization = break language into tokens.  
- Embeddings = give tokens *meaning in math*.  
- Attention = decide what matters most.  

🎉 With these 3, you unlock the **power of modern NLP** (Transformers, GPT, etc.).  
