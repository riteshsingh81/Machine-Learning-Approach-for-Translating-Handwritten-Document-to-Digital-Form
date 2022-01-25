# Machine-Learning-Approach-for-Translating-Handwritten-Document-to-Digital-Form
Handwritten character recognition is a field of research in artificial intelligence, computer vision, and pattern recognition. A computer performing handwriting recognition is said to be able to acquire and detect characters in paper documents, pictures, touch-screen devices and convert them into machine-encoded form. We will look into the SVMs and kNN techniques to solve the problem.

Metrics
We will be using the accuracy score to quantify the performance of our model. The accuracy will tell us what percentage of our test data was classified correctly. The accuracy is a good metric choice because it will be easy to compare our model’s performance to that of the benchmark as it uses the same metric. Also, our dataset is balanced (equal number of training examples for each label) which makes the accuracy appropriate for this problem.


Data Exploration
Some initial data exploration reveals that our training set contains 42,000 samples in total and 784 features. Each sample in the dataset represent an image that is 28 pixels in height and 28 pixels in width, hence the total of 784 pixels. Each image is labelled with their corresponding category that is the actual digit from 0 to 9 for a total of 10 different labels.

Algorithms and Techniques
It has been shown that Support Vector Machines (SVMs) can be applied to image and hand-written character recognition [4]. SVMs are effective in high dimensional spaces, hence it makes sense to use SVMs for this study given the high dimensionality of our input space, i.e. 784 features. However, SVMs don’t perform well in large datasets as the training time becomes cubic in the size of the dataset. This could be an issue as our dataset containing 42,000 samples which is quite large. To deal with this issue, we will adopt a technique proposed by a study conducted at the University of California, Berkeley, which is to train a support vector machine on the collection of nearest neighbours in a solution they called “SVM-KNN” [2]. Training an SVM on the entire data set is slow and the extension of SVM to multiple classes is not as natural as Nearest Neighbor (NN). However, in the neighbourhood of a small number of examples and a small number of classes, SVMs often perform better than other classification methods.
We use NN as an initial pruning stage and perform SVM on the smaller but more relevant set of examples that require careful discrimination. This approach reflects the way humans perform coarse categorization: when presented with an image, human observers can answer coarse queries such as presence or absence of an animal in as little as 150ms, and of course, can tell what animal it is given enough time [6]. This process of a quick categorization, followed by successive finer but slower discrimination was the inspiration behind the “SVM-KNN” technique.
Benchmark
To benchmark our model, we will use kaggle’s benchmark model which uses a simple random forest model to make predictions on the test set. The benchmark model accuracy score is 0.93, ​in other words, it is able to correctly label 93%​ of the test data. We will evaluate how this model compares to our final solution. We will aim to have our final solution have an accuracy higher than the benchmark model.
Data Preprocessing
Since the original dimension is quite large (784 input features), the dimensionality reduction becomes necessary. First, we extract the principal components from the original data. We do this by fitting a Principle Component Analysis (PCA) on the training set, then transforming the data using the PCA fit. We used the PCA module of the scikit-learn Python library with n_components​ set to 60​ to transform the dataset. As shown in the figure below, the first 60 ​principal components can interpret approximately 97% of total information (in terms of total variance retained), which suffice to be representative of the information in the original dataset. We thus choose the first 60 principal components as the extracted features.


Implementation
Our simple implementation of SVM-KNN goes as follows: for a query, we compute the Euclidean distances of the query to all training examples and pick the K nearest neighbours. If the K neighbours have all the same labels, the query is labelled and exit. Else, we compute the pairwise distances between the K neighbours, convert the distance matrix to a kernel matrix and apply multiclass SVM. We finally use the resulting classifier to label the query.


Model Evaluation and Validation
In our initial implementation, we extract 60 principal components and use parameters values of k=2 for KNN and C=1.0 for SVM. During development, a validation set was used to evaluate the model. I split the dataset into training and test sets. The final hyperparameters were chosen because they performed the best amongst the tried combinations. A final value of k=3 and C=0.5 yielded the best results. A low k value makes sense for our model because we are trying to find the few samples where NN has a hard time establishing a decision boundary and apply SVM to perform a more coarse-grained classification.
To verify the robustness of the final model, I use a cross-validation technique (StratifiedShuffleSplit) on the dataset to ensure that the model generalizes well by using the entire dataset for both training and testing. The model consistently categorized the handwritten characters with a 97% accuracy.
