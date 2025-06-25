# generative pre-trained transformers

the original transformers were designed with the intention of translating text from one language to another, as such they consisted of 2 major architectural components an *encoder* and *decoder*

the **encoder** processes the input text and encodes it into a series of vectors to capture contextual input

the **decoder** module takes the encoded vectors and generates the output text

in contrast to the original transformers, the general gpt architecture is only the decoder

decoder styles modles like gpt generate text by predicting one word at a time, they are considered a type of *autoregressive* model, since they incorporate their previous outputs as inputs for future predictions

there are typically 2 stages in creating an llm, *pre-training* and *fine-tuning*

in **pre-training**, we first train it on a large corpus of *raw* text data, where the data is **un-labelled**, aiming to allow the model to develop a broad understanding of language

in this phase, llms use **self-supervised learning**, where the model generates its own labels from the input data

in **fine-tuning**, the model is trained on a narrower dataset that is more specific to particular tasks or domains

there are 2 popular catergories of fine-tuning: *instruction fine-tuning* and *classification fine-tuning*

in instruction fine-tuning the labeled dataset consists of instruction-answer pairs

in classification fine-tuning, the labeled dataset consists of text and associated class labels
