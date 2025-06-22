
# Contributing to AWS Quiz Collection

👋 Welcome! Thank you for your interest in contributing to the **AWS Quiz Collection**. This repository is a curated set of questions and answers on AWS services, designed to help individuals prepare for interviews, certifications, and deepen their cloud knowledge.

We welcome contributions from AWS professionals, learners, and enthusiasts.

---

## 📋 Table of Contents

1. [How to Contribute](#how-to-contribute)
2. [Guidelines for Writing Questions](#guidelines-for-writing-questions)
3. [Folder and File Structure](#folder-and-file-structure)
4. [Markdown Style Guide](#markdown-style-guide)
5. [How to Submit a Pull Request](#how-to-submit-a-pull-request)
6. [Code of Conduct](#code-of-conduct)
7. [Need Help?](#need-help)

---

## 💡 How to Contribute

You can contribute in several ways:

- 📄 Add new questions and answers for an existing AWS service.
- ➕ Add a new AWS service quiz file (e.g., CloudTrail, SQS, EKS).
- ✏️ Fix typos or improve explanations.
- 📚 Add references or links to official AWS documentation.
- 🧪 Add real-world interview-style questions.

---

## 🧠 Guidelines for Writing Questions

Please follow these principles when writing or editing quiz content:

1. **Question Format:**  
   Use a simple, clear question. Avoid ambiguity.

2. **Answer Format:**  
   Provide a **concise but complete** answer. Include bullet points, code snippets, or diagrams if necessary.

3. **Difficulty Tags (Optional):**  
   Add `(Beginner)`, `(Intermediate)`, or `(Advanced)` before the question to help readers filter.

4. **References (Optional but Encouraged):**  
   Link to AWS documentation for deeper reading.

**Example Format:**

```markdown
## 3. What is the difference between an IAM role and an IAM user? (Beginner)

**Answer:**

- **IAM User**: A person or application with long-term credentials (username/password or access keys).
- **IAM Role**: An AWS identity with **temporary** credentials, typically assumed by trusted entities (e.g., EC2, Lambda, another user).

👉 [Read More](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles.html)
📁 Folder and File Structure
All service-specific quizzes are placed under the quizzes/ directory:

quizzes/
├── 01_IAM.md
├── 02_EC2.md
├── 03_S3.md
├── 04_VPC.md
├── 05_RDS.md
├── ...
File Naming Convention: NN_ServiceName.md (where NN is a 2-digit number for sorting)

New Services: Follow the same naming convention and create a new .md file inside quizzes/.

✍️ Markdown Style Guide
Use ## for each question heading.

Put the Answer below the question, using bold **Answer:** prefix.

Use --- to separate Q&A sections.

Use 👉 or bullet points (-, *) to organize lists clearly.

Wrap code in triple backticks:

aws iam list-users
🔁 How to Submit a Pull Request
Fork this repository.

Create a new branch (e.g., add-sqs-quiz).

Make your changes locally.

Commit your changes with a clear message.

Push to your fork.

Create a Pull Request (PR) to the main branch of this repo.

Wait for review or feedback!

Please limit each PR to one topic or service to keep reviews focused and manageable.

🙌 Code of Conduct
We follow the Contributor Covenant Code of Conduct.

Be respectful and inclusive.

Avoid personal attacks or political discussions.

Be open to feedback and collaboration.

🆘 Need Help?
Feel free to open an issue if:

You have a question about contributing.

You want to suggest a new category or idea.

You’re unsure where your contribution fits.

Let’s build a great AWS knowledge resource together!

Happy Learning & Contributing! 🚀
