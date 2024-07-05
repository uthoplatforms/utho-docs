---
slug: Cyber-Attack-Detection-using-Machine-Learning
description: Discover various machine learning cyber attacks such as evasion, poisoning, and inference attacks, and explore the specific aspects of ML applications they target in this informative guide
keywords: ['machine learning cyber attacks','evasion attacks against machine learning']
published: 2024-03-13
modified_by:
  name: Utho
title: "The Top Cyber Attacks Targeting Machine Learning Applications"
title_meta: "Cybersecurity Threats Against Machine Learning Systems"
authors: ["Pawan Kumar"]
---
[Machine learning (ML)](/docs/guides/history-of-machine-learning/) algorithms and models process vast amounts of data to make predictions and adjustments based on patterns they recognize. ML is behind various applications like chatbots, product recommendations, self-driving cars, and assists in decision-making in sectors like health and finance. With tools and frameworks like [TensorFlow](/docs/guides/how-to-install-tensorflow/) and PyTorch [PyTorch](/docs/guides/pytorch-installation-ubuntu-2004/) becoming more accessible, developers can integrate ML into their applications more easily. However, before diving into machine learning, it's crucial to understand the common cyber attacks targeting ML systems. When considering the security of your ML application, you need to focus on the following areas:
- **Data**: If your data is corrupted in any way, you will not obtain reliable or useful results from your machine learning models.
- **Application**: If a model gets corrupted, even with flawless data, it will produce inaccurate results.
- **Output**: An application only gives the output it's supposed to. Changing it to do something else is not how it's meant to be used.
- **User**: Even if everything else is secure, users can still mess up machine learning applications by giving bad input or misunderstanding the output.

This guide talks about the main security problems you might face in a machine learning project. Some of these are common to all software projects, while others are specific to machine learning.

## Evasion

The evasion attack poses a significant threat to machine learning applications. It involves altering input data to deceive ML classifiers. For instance, a successful evasion attack might tweak an image slightly, causing it to be misclassified by the ML algorithm. Evasion attacks aim to infiltrate systems through various means:

- **Attachment**:  Malicious code executes upon opening a file attachment.
- **Link**: Malicious code executes upon accessing a linked resource.
- **Image**: Malicious code is triggered when viewing an image.
- **Spoofing**: Hackers impersonate trusted entities.
- **Biometric**: Attackers mimic facial expressions or fingerprints to bypass security.
- **Specially crafted code**: Machine learning models can be trained to manipulate the output of target models.

## Poisoning

A poisoning attack involves injecting false information into an application's data stream to produce inaccurate results. Here are the common scenarios where poisoning may occur:

- During model training, unreliable or unvetted data sources can introduce inaccuracies.
- After model training, large amounts of biased input can skew results.

Attackers typically prefer stealth in poisoning attacks, aiming to alter outputs in their favor rather than bringing down the system. SVM classifiers are often targeted for tasks like redrawing political or sales boundaries or giving certain products an advantage in sales campaigns.

## Inference

An inference attack exploits knowledge about the dataset used to train a machine learning model. By analyzing the data, attackers can uncover vulnerabilities. This attack is most effective on overfitted models, where the model closely follows the original data points. It mainly targets supervised learning models and Generative Adversarial Networks (GANs).

During the attack, hackers send queries to the model, which generates predictions based on confidence levels for each supported class. This provides valuable insights into the application. Unfortunately, this attack is often used to target individuals and their sensitive data, such as medical records.

## Trojans

A trojan is a deceptive piece of code or data that appears legitimate but is actually designed to take control of an application or manipulate its components. In the context of machine learning, trojans often remain hidden and aim to gather information about the application's data rather than causing obvious harm, such as deleting files. Here are some common types of trojan attacks in the realm of machine learning:

- **Backdoor**: Establishes a hidden entry point in the target system, allowing hackers remote access to control it. This access enables various malicious actions, such as downloading datasets/models, corrupting data, or altering model behavior.
- **Banker**:  Focuses on acquiring or tampering with financial information. In machine learning, this trojan may target data acquisition (e.g., membership inference) or evasion to obtain credentials, aiming to persuade users to download malicious payloads.
- **Downloader**: Targets already compromised systems to download additional malware. This malware can have diverse impacts, including compromising data integrity or system performance.
- **Neural**: Embeds malicious data into the dataset to trigger specific actions or conditions. This trojan may manipulate neural network weights to affect only certain nodes, particularly effective against Convolutional Neural Networks (CNNs), LSTM, and RNNs.

## Backdoors

This attack exploits vulnerabilities in system, application, or data streams to infiltrate the underlying system or application without proper security credentials. Unlike other attacks, the focus is on corrupting the neural network itself rather than manipulating inputs. Similar to a trojan, the backdoor attack involves altering training data to gain unauthorized access to the model, typically through the neural network. Due to its subtlety, detecting and mitigating this attack often requires a separate application.

## Espionage

An espionage attack involves stealthily stealing classified or sensitive data, including intellectual property, to gain an advantage over individuals, groups, or organizations. The attacker's aim is to remain undetected for an extended period to spy on an organization's activities and achieve specific goals. This type of attack may unfold gradually over years, with subtle outcomes such as redirecting some sales towards a particular product. Machine learning data and models can be targeted in this manner, with attackers using predictive models to sift through logs for specific access patterns to locate valuable data.

## Sabotage

Sabotage involves intentional and malicious actions aimed at disrupting normal processes, causing them to malfunction even if the data remains intact and unbiased. While sabotage can be easily detected, by the time it's identified, the damage is often already done. Financial institutions, in particular, are vulnerable to sabotage due to the vast amounts of data involved in creating and managing models. Fixing sabotage can be challenging as it requires remediation of the underlying data before rebuilding the model.

## Fraud

Fraud happens when hackers use tactics like phishing or communication from unfamiliar sources to compromise system, application, or data security covertly. This unauthorized access enables them to exploit the application or manipulate results, such as spreading false election predictions. Luckily, ongoing research utilizes machine learning methods to detect and combat fraud effectively.

## Conclusion

Before integrating machine learning (ML) into your project, it's essential to understand the types of cyber attacks frequently aimed at ML-powered applications. Common attacks include evasion, poisoning, and inference. While trojans, backdoors, and espionage are used across various applications, they're leveraged differently against machine learning systems. Now that you're aware of these cyber threats, you can begin developing an ML-powered application, such as by installing TensorFlow on Ubuntu 20.04.
