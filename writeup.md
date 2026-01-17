# Winter Project: Email Phishing Detection
**Name:** Ruwan Thoumoung
**Email:** thoumoun@usc.edu

## Project Overview
This project uses transfer learning from a BERT transformer model to classify emails as either 'Legitimate' or 'Phishing/Spam' based on the content of their body text. By fine-tuning a pre-trained language model, I attempted to capture semantic cues that distinguish legitimate communication from malicious intent.

## Dataset
**Dataset Used:** Kaggle Phishing Email Dataset
**Preprocessing:**
* I concatenated the 'body' and 'label' columns from the following CSVs:
CEAS_08.csv,
Enron.csv
Ling.csv
Nazario.csv
Nigerian_Fraud.csv
SpamAssasin.csv
* I did not use phishing_email.csv
* The text was tokenized using the BERT tokenizer with a maximum sequence length of 128 tokens.
* This meant the entire body of emails could not be read.
* Reasoning was that spam emails can generally be recognized from the first few sentences
* So I thought tradeoff of memory efficiency for less data would not be detrimental to performance.
* Data was split into a 80% training set, 10% validation set, and 10% test set.

## Model Development
**Architecture:**
* I utilized the `BertForSequenceClassification` model from Hugging Face, which places a linear classification layer on top of the pre-trained BERT base.
* **Why BERT?** BERT (Bidirectional Encoder Representations from Transformers) captures deep contextual relationships in text, making it highly effective for understanding the nuances between legitimate emails and phishing attempts compared to simpler RNNs.

**Hyperparameters:**
* **Batch Size:** 32
* **Learning Rate:** 2e-5
* **Epochs:** 3
* **Optimizer:** AdamW (Adam with weight decay fix)

## Results
**Training Performance:**
* The model converged quickly, achieving **99% validation accuracy** after just the first epoch.
* By Epoch 3, the training loss dropped to nearly **0.00**, indicating the model had fully learned the training distribution.

**Evaluation Metrics (Test Set):**
* **Test Accuracy:** [99.28%]
* The hight test accuracy implies that the model should generalize well to out of sample data.
* **Test Loss:** 0.04
| Class | Precision | Recall | F1-Score | Support |
| :--- | :--- | :--- | :--- | :--- |
| **Legitimate** | 0.99 | 0.99 | 0.99 | 3,947 |
| **Spam** | 0.99 | 0.99 | 0.99 | 4,302 |

* **High Precision (0.99 for Spam):** This indicates that when the model flags an email as phishing, it is almost certainly correct. This is crucial for user trust; false positives (flagging a boss's email as spam) can be disruptive.
* **High Recall (0.99 for Spam):** This indicates that the model successfully catches 99% of all actual phishing attacks in the dataset.
* **Balanced F1-Score:** The high F1-score confirms that the model is robust and performs equally well on both legitimate and malicious emails, rather than being biased toward one category.


## Discussion
**1. Fit:**
The BERT architecture proved highly effective for this task. The rapid convergence to 99% accuracy suggests that the pre-trained knowledge in BERT transfers very well to phishing detection. This was probably because phishing relies on specific language patterns that BERT could easily recognize.

**2. Social Implications:**
Accurate phishing detection significantly contributes to social good by protecting individuals from financial loss and identity theft. Automating this process reduces the cognitive load on users and intercepts malicious actors before they can cause harm.

**3. Limitations:**
* **Overfitting Risk:** With a training loss of 0.00, the model might be overfitting to specific keywords in this dataset. It would need to be tested on real-world, evolving phishing emails to ensure robustness.
* **Context Missing:** The model relies solely on text body, ignoring other crucial phishing indicators like sender address, header metadata, or embedded URL analysis.
* **Entire body not used:** Since the entire bodies of the emails used too many tokens, they had to be truncated. So this model in particular would likely undeprerform if it had to detect spam emails with revealing information later on in the body.

**4. Future Steps:**
If I were to continue this project, I would:
1.  Integrate more features (sender domain, subject) alongside the text model.
2.  Test the model against "diffcult examples" (emails designed to trick AI) to see if it remains robust. This should help the model generalize better.
