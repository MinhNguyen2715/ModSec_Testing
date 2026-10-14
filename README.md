# Code-Injection

## Kết quả chạy baseline 
| Methods | Naïve Bayes | Decision Tree | Random Forest |  Logistic Regression | AdaBoost |
| :---: | :---: | :---: | :---: | :---: | :---: |
| BOW | 0.83 | 0.96 | 0.96 | 0.95 | 0.39 |
| TF-IDF | 0.87 | 0.96 | 0.96 | 0.95 | 0.46 |
| Word2Vec | 0.48 | 0.94 | 0.95 | 0.62 | 0.69 |

- Kết quả chạy baseline: Baseline.ipynb
- Hiện tại đang có một số vấn đề:
  + Data imbalance
  + Word2Vec có kết quả không tốt khi kết hợp với các bộ classifiers thuần và cần thử nghiệm thêm với một vài neural classifiers (CNN,...)

## Note về CAPEC data
- Data được phân tích và xử lý trong file: Data_Processing.ipynb
- Có 2 trường attack ko có tấn công (33 - HTTP Request Smuggling và 248 - Command Injection) trong bộ dataset multiclass. Lý do có thể vì hai trường attack này giao với các trường attack khác (multilabel)
- Tổng cộng các loại tấn công (đã bỏ 2 loại attack bên trên): 12 (bao gồm normal)
000 - Normal: 226509
272 - Protocol Manipulation: 6924
242 - Code Injection: 13792
88 - OS Command Injection: 3074
126 - Path Traversal: 17595
66 - SQL Injection: 248093
16 - Dictionary-based Password Attack: 836 X
310 - Scanning for Vulnerable Software: 2382
153 - Input Data Manipulation: 1387
274 - HTTP Verb Tampering: 4047 X
194 - Fake the Source of Data: 55982
34 - HTTP Response Splitting: 19134
- Mỗi sample có rất nhiều trường dữ liệu, tuy nhiên chỉ có 1 số attribute giúp classify web attacking
- Thử nghiệm với concat 3 trường dữ liệu: request_method, request_http_request và request_body (với method POST)
- Thử nghiệm với một vài kiểu tấn công, và để lại một vài kiểu tấn công có cùng variance với bộ dataset để nghiên cứu Transfer Learning
- Các kiểu tấn công sử dụng trong dataset: 272 - Protocol Manipulation, 242 - Code Injection, 126 - Path Traversal, 66 - SQL Injection, 310 - Scanning for Vulnerable Software, 153 - Input Data Manipulation, 194 - Fake the Source of Data, 34 - HTTP Response Splitting
Kiểu tấn công dành cho Transfer Learning: OS Command Injection
- Hiện tại đang chưa tích hợp bộ dataset CSIC 2010-2012 (Bao gồm XSS attack và SQLi attack)

## Các loại tấn công
- 66 - SQL Injection: This attack exploits target software that constructs SQL statements based on user input. An attacker crafts input strings so that when the target software constructs SQL statements based on the input, the resulting SQL statement performs actions other than those the application intended. SQL Injection results from failure of the application to appropriately validate input.
- 194 - Fake the Source of Data: An adversary takes advantage of improper authentication to provide data or services under a falsified identity. The purpose of using the falsified identity may be to prevent traceability of the provided data or to assume the rights granted to another individual. One of the simplest forms of this attack would be the creation of an email message with a modified "From" field in order to appear that the message was sent from someone other than the actual sender. The root of the attack (in this case the email system) fails to properly authenticate the source and this results in the reader incorrectly performing the instructed action. Results of the attack vary depending on the details of the attack, but common results include privilege escalation, obfuscation of other attacks, and data corruption/manipulation.
- 34 - HTTP Response Splitting: An adversary manipulates and injects malicious content, in the form of secret unauthorized HTTP responses, into a single HTTP response from a vulnerable or compromised back-end HTTP agent (e.g., web server) or into an already spoofed HTTP response from an adversary controlled domain/site.
- 126 - Path Traversal: An adversary uses path manipulation methods to exploit insufficient input validation of a target to obtain access to data that should be not be retrievable by ordinary well-formed requests. A typical variety of this attack involves specifying a path to a desired file together with dot-dot-slash characters, resulting in the file access API or function traversing out of the intended directory structure and into the root file system. By replacing or modifying the expected path information the access function or API retrieves the file desired by the attacker. These attacks either involve the attacker providing a complete path to a targeted file or using control characters (e.g. path separators (/ or \) and/or dots (.)) to reach desired directories or files.
- 242 - Code Injection: An adversary exploits a weakness in input validation on the target to inject new code into that which is currently executing. This differs from code inclusion in that code inclusion involves the addition or replacement of a reference to a code file, which is subsequently loaded by the target and used as part of the code of some application.
- 272 - Protocol Manipulation: An adversary subverts a communications protocol to perform an attack. This type of attack can allow an adversary to impersonate others, discover sensitive information, control the outcome of a session, or perform other attacks. This type of attack targets invalid assumptions that may be inherent in implementers of the protocol, incorrect implementations of the protocol, or vulnerabilities in the protocol itself.
- 310 - Scanning for Vulnerable Software: An attacker engages in scanning activity to find vulnerable software versions or types, such as operating system versions or network services. Vulnerable or exploitable network configurations, such as improperly firewalled systems, or misconfigured systems in the DMZ or external network, provide windows of opportunity for an attacker. Common types of vulnerable software include unpatched operating systems or services (e.g FTP, Telnet, SMTP, SNMP) running on open ports that the attacker has identified. Attackers usually begin probing for vulnerable software once the external network has been port scanned and potential targets have been revealed.
- 153 - Input Data Manipulation: An attacker exploits a weakness in input validation by controlling the format, structure, and composition of data to an input-processing interface. By supplying input of a non-standard or unexpected form an attacker can adversely impact the security of the target.

## Combine các loại tấn công giống nhau:
1. 66 + 242
2. 272 + 153
3. 194
4. 34
5. 126
6. 310

## Data kiểm thử cho Transfer learning (Đã combine OS Command Injection và Multilabel)
- OS Command Injection 
- Multilabel samples
- XSS Injection

## Kết quả multiclass đã combine:
- TF-IDF + NB: 87.5
- TF-IDF + RF: 96
- LSTM: 97.5

## Kết quả detect với data Transfer learning:
- TF-IDF + NB: 82.1
- TF-IDF + RF: 68.7
- LSTM: 84.4

## To do:
- Đánh giá thuộc đúng nhóm tấn công nào **
- Chạy thử Transfer Learning để đánh giá **
- Mod Sec ***
- Tìm hiểu áp dụng GAN **

## Kết quả phân loại tập dữ liệu transferlearning
- Tập nguồn: dataset_capec_combine.csv
- Tập đích: dataset_capec_transfer.csv
- Model được train trên tập nguồn và đánh giá trên tập đích

| Methods | Naïve Bayes | Decision Tree | Random Forest |  Logistic Regression | AdaBoost |
| :---: | :---: | :---: | :---: | :---: | :---: |
| TF-IDF | 0.4 | 0.39 | 0.35 | 0.37 | 0.3 |

## Kết quả Modsecurity
- Vì lượng data rất lớn (600000 samples), mỗi lần infer một url trung bình 3s, vì vậy mới chạy kết quả trên 7000 samples bị tấn công
- Phân loại data thành 4 phần (cho dễ chạy): normal_get, normal_post, attack_get, attack_post
- Kết quả ở đây là xác định ModSec có phát hiện được tấn công hay không (binary task)
- Kết quả thử nghiệm trên 7000 samples của tập data attack_get: 6% 

## Kết quả chạy thử nghiệm Transfer Learning (áp dụng kĩ thuật GAN)
- Tập nguồn: dataset_capec_combine.csv
- Tập đích: dataset_capec_transfer.csv
- Model được train supervised trên tập nguồn và unsupervised trên tập đích
- Kết quả thử nghiệm trên 1 epoch (chưa fine-tune tham số): 0.52

## To do
- Modsec
- MMD
- Deeplearning
- Fine tune Bert


## Dataset link: https://drive.google.com/drive/folders/1I8fS2uSv4v3tlmVcuzzC07QTHWjFOCUr

## Kết quả ModSecurity trên từng kiểu tấn công

| Attacks | Precision | Recall | F1-Score |
| :---: | :---: | :---: | :---: |
| Injection | 0.83 | 0.76 | 0.79 |
| Fake the source of data | 0.82 | 0.74 | 0.77 |
| HTTP splitting | 0.81 | 0.73 | 0.76 |
| Path traversal | 0.80 | 0.71 | 0.75 |
| Manipulation | 0.62 | 0.51 | 0.56 |
| Scanning software | 0.78 | 0.72 | 0.74 |

## Kết quả sử dụng phương pháp GAN trong xác định kiểu tấn công mới với các bộ trích xuất đặc trưng

| TF-IDF | BOW | WORD2VEC | BERT (Feature extractor) | BERT (Fine-tuning)
| :---: | :---: | :---: | :---: | :---: |
| 66.40 | 59.32 | 61.49 | 64.55 | 70.21 |

- Trong quá trình training, Finetune BERT với GAN có một vài vấn đề như sau:

  - Bert (Feature extractor) là đang dùng Discriminator (D) với đầu vào là hidden states của BERT, Generator (G) cố gắng tạo ra embedding của BERT để đánh lừa D. Nếu muốn finetune BERT, mình cần đưa BERT vào D, như vậy làm cho mô hình của D phức tạp hơn G nhiều => D luôn tốt hơn G
  - Nếu muốn kết quả tốt nhất (có khả năng tốt nhất) thì ko làm việc trên embedding nữa. Đưa BERT vào cả D và G cho cân bằng, G ko tạo embedding nữa mà tạo hẳn ra câu luôn, do đó D cũng nhận vào 1 câu. Cái này làm cho BERT có thể finetune đc, mà 2 D và G cũng ngang nhau. Nhưng mà train phần này sẽ khó, tại vì phải train 2 BERT, mà chưa chắc đã hội tụ (loss bao gồm loss(bert) + loss(G) + loss(D)) + bản thân việc train model của GAN đã rất khó + thời gian train rất lâu + tốn memory
  - Thử nghiệm hiện tại là cả G và D đang dùng chung 1 BERT để fine-tune, tuy nhiên như đã nói ở trên thì việc hội tụ rất khó và rất lâu + rất tốn mem (bản chất training GAN model đã khó + fine-tune BERT -> khó hơn nữa). Kết quả tốt nhất hiện nay là đạt 70% accuracy

- Có thể tìm một vài hướng khác để thử nghiệm thêm, vì tối ưu GAN hiện tại cũng đang khá khó

