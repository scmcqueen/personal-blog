+++
date = '2024-12-09T22:06:02-07:00'
draft = false
title = 'Spotify Collaboration Network Analysis'
+++

## Trending Tunes: A networks-based approach to anticipating what songs will go viral based on the artist collaboration network

Mariam Joseph, Luna Liu, Skyeler McQueen, Julia Zhang

December 2024, For Networks SI 608 at the University of Michigan


Link to Github Repo with Code & Data: [https://github.com/scmcqueen/SI608NetworksProject](https://github.com/scmcqueen/SI608NetworksProject)

[PDF of Paper](https://github.com/scmcqueen/SI608NetworksProject/blob/72484d88907544d9f313d1b9859ad78e5fc515e7/608%20Final%20project%20Report.pdf) & [Slideshow](https://github.com/scmcqueen/SI608NetworksProject/blob/72484d88907544d9f313d1b9859ad78e5fc515e7/608%20Final%20Presentation.pdf)

### Research Question

Can we use artists’ collaboration networks to predict trending Instagram Reels songs? Will this method outperform random sampling?

### Motivation
The rise of short-form videos on platforms like TikTok, YouTube Shorts, and Instagram Reels has introduced a new way for users to consume content. Content creators of these short videos frequently use trending music as their background score. Therefore, to better understand what makes an artist's music a top trending background score for short videos, we propose the following question: can we predict the next top trending Instagram reel music based on previous artists' collaboration? This proposed question paves a pathway to exploring the impact of collaboration between artists and music trends' influence on social media. Researchers have previously analyzed the collaboration networks in other domains  Conducting an inference study on the characteristics of successful trending music and artists' collaboration patterns would provide invaluable insights for current and upcoming artists looking to create their next big viral music.

### Related Work

The concept of using networks to predict trends is well-established in research. Newman’s study “The Structure of Scientific Collaboration Networks” (2001) showed that "small world" properties, where individuals are separated by short paths, are common in collaboration networks. This insight supports the idea that close connections within a network, such as those between artists, can influence widespread reach and impact.
Adamic and Adar’s “Friends and Neighbors on the Web” (2003) expanded on network analysis by introducing a method to predict link formation based on shared neighbors, which could be applied to predicting connections in a music collaboration network. This metric assesses how mutual connections can signal new links, potentially helping identify which artists or songs might achieve popularity.
In more recent work, Luo et al. used graph neural networks (GNN) to predict group membership in bipartite networks in “Group link prediction in bipartite graphs with graph neural networks (2025). We could adapt this method to track and anticipate the songs likely to enter trending playlists on social media. Their method focuses on link prediction within networks, where the "top 100" playlist could represent a significant group node, connecting songs through collaborative or co-featured artists. By applying similar GNN techniques and the Adamic–Adar index, we can explore how collaboration patterns between artists contribute to trending music on platforms like Instagram Reels. This study will build upon these foundational insights, using both network-based link prediction and GNNs to examine if collaboration structures can anticipate the next viral track, offering valuable guidance for artists aiming for social media success.

### Datasets
The data is generated from the Spotify API. Each node is an artist and there is an edge between the two nodes if the artists collaborated on an album before. We sampled the Instagram Reel Top 100 Playlist and then also completed a random sample of artists to serve as our baseline. After these initial samples, we queried the seed nodes’ collaborators and their collaborators. Note that the edges generated from collaborations are weighted by the number of collaborations (albums) that the artists have published on Spotify together. Please refer to the appendix for an example image to help explain this strategy. We collect metadata on each artist: their followers, popularity, and genre. There is a table explaining the data in the appendix.

Data limitations: The Spotify API has a limit of how many albums (and thus collaborators) one can query. We can have a maximum of 50 collaborators per artist (most recent first). For a majority of the artists queried, this limitation poses no problem. For a few famous artists (e.g. Sabrina Carpenter and Harry Styles), we are limiting the number of edges a node can have.

### Data Collection
We used the Spotify API to query the “Instagram Reels - Top Trending 2024 | NEW VIRAL HIT SONGS ON THE INTERNET” playlist, which is updated once a week. The query fetches all of the artists, their collaborators, and their collaborators’ collaborators. The script for this process is included in our GitHub. Additionally, we completed a pseudo-random query of artists, based on Perry Janssen’s random track script (Janssen 2019). This query fetches random artists and their first and second-layer collaborators.  The Instagram Reels playlist is updated once a week, so we will be completing this querying process once a week in order to see how the network evolves over time.

### Methods & Results
We attempted four methods for predicting whether an artist will be featured in the Instagram Reels Trending Tunes playlist: ML Models with & without network features, ML Model with node embeddings, Community detection via clique percolation, and graph neural networks. In the following section, we will outline how we conducted these models and their results.

#### ML Model With & Without Network Features
To evaluate whether artist collaboration networks improve the prediction of trending Instagram Reels songs, we developed two machine learning models:
1. Model Without Network Features: Focused on artist metadata such as popularity, followers, and collaborator-based features (e.g., number of collaborators and their popularity).
2. Model With Network Features: Included additional features derived from network analysis, such as PageRank, clustering coefficient, degree centrality, and closeness centrality.

##### Steps:
1. Data Collection and Preprocessing:
- For Model 1, we derived features such as the number of collaborators and the aggregated popularity of collaborators.
- For Model 2, we enhanced the dataset with network features, leveraging graph algorithms to calculate metrics like PageRank and clustering coefficient.
2. Oversampling to Address Class Imbalance:
- The target variable (in_playlist) was highly imbalanced. To handle this, I used SMOTE (Synthetic Minority Oversampling Technique), which generates synthetic samples for the minority class to balance the dataset.
3. Feature Scaling:
- Numerical features were standardized using StandardScaler to ensure all features had equal importance.
4. Model Selection and Training: 
- We trained the following models:
  - Random Forest Classifier
  - XGBoost Classifier
  - LightGBM Classifier
- Models were evaluated using the F1 Score, prioritizing a balance between precision and recall.
5. Hyperparameter Tuning:
For Model 2, we used Grid search to fine-tune the Light GBM model. Key hyperparameters, such as the number of estimators, maximum depth, and splitting criteria, were optimized.

##### Performance:

| Metric    | Model 1 | Model 2 |
| --------- | ------: | ------: |
| Accuracy  | 0.98017 | 0.99009 |
| Precision | 0.19231 | 0.46154 |
| Recall    | 0.35714 | 0.42857 |
| F1 Score  | 0.25000 | 0.44444 |


##### Key Observations:
Both models prioritize popularity, reflecting the importance of individual artist metrics and network proximity in predicting playlist inclusion. Model 2, with its richer feature set, like closeness_cent, underscores the predictive power of collaboration-based and network-driven metrics.

Network-specific features like deg_cent (degree centrality) and clustering contribute substantially to Model 2, highlighting how these relational metrics add explanatory power. The feature degree has zero importance in Model 2, likely because its information is redundant when other network features (e.g., deg_cent) are already included.
Feature importance in Model 2 is distributed across a broader range of metrics, suggesting a more nuanced model capable of balancing multiple factors. By capturing both individual artist characteristics and their network relationships, Model 2 develops a deeper understanding of what drives playlist inclusion.

##### Advantages of Model 2:

By combining individual and network metrics, Model 2 can identify less obvious candidates for playlist inclusion, such as artists in influential communities or with high collaborative potential. Playlists often feature artists based on network effects (e.g., collaborations, community influence), and Model 2 reflects this reality, leading to more accurate and practical predictions. The distributed importance across features allows Model 2 to rely less on any single metric, making it more robust and adaptable.

##### How Network Features May Improve Predictions:

Network metrics like closeness centrality highlight how closely an artist is connected to others in the network. Artists with higher centrality may have greater exposure and influence, increasing their likelihood of playlist inclusion. Clustering coefficients reveal the density of connections around an artist, indicating whether they are part of tightly-knit communities. Such artists might benefit from collaborative promotion and shared fan bases, making them more attractive for playlists.

##### Why Networks are Beneficial:

Network-based features provide additional context about an artist’s position within the ecosystem. This allows the model to capture dynamics that are not apparent from individual metrics like popularity or follower count. Playlists often feature artists who collaborate frequently or belong to influential communities. Network metrics mimic these real-world interactions, making predictions more aligned with human decision-making. Combining traditional features (e.g., popularity, followers) with network features creates a richer dataset, enabling the model to identify complex patterns and interactions that influence playlist inclusion.

##### Potential Reasons for Improvement:

Playlists are curated based on both individual artist metrics and their relationships within the music community. Network-based features directly reflect these relationships, helping the model identify artists who might benefit from collaborative exposure or network-driven popularity.

Networks inherently account for indirect influence, such as an artist being connected to others who are already prominent. This secondary influence is often a factor in real-world playlist curation, where collaborations and associations play a role.

By integrating these features, Model 2 captures a more holistic view of the factors influencing playlist inclusion, explaining its superior performance compared to the simpler Model 1, which relies only on individual artist metrics. This highlights the critical role of network analysis in understanding and predicting collaborative, community-driven phenomena in music streaming.

#### Community Detection and Alignment with the in_playlist Feature
To explore the relationship between community structures and the in_playlist feature, we conducted community detection on weekly networks using the clique percolation method with varying values of k. The results provide insights into the granularity of the detected communities, their alignment with the in_playlist feature, and the temporal evolution of the network structure.
We performed community detection on the large network and analyzed how these communities matched the in_playlist feature. The specific results are as follows:

- **Week 1**
  - **Clique Percolation (k = 3)**
    - Discovered **191** communities
    - Average community size: **16.69** nodes
    - Pairwise accuracy: **0.896** (alignment between communities and the `in_playlist` feature)
  - **Clique Percolation (k = 4)**
    - Discovered **115** communities
    - Average community size: **10.09** nodes
    - Pairwise accuracy: **0.972**

- **Week 2**
  - **Clique Percolation (k = 3)**
    - Discovered **187** communities
    - Average community size: **16.94** nodes
    - Pairwise accuracy: **0.896**
  - **Clique Percolation (k = 4)**
    - Discovered **113** communities
    - Average community size: **10.20** nodes
    - Pairwise accuracy: **0.972**

- **Week 3**
  - **Clique Percolation (k = 3)**
    - Discovered **185** communities
    - Average community size: **17.09** nodes
    - Pairwise accuracy: **0.898**
  - **Clique Percolation (k = 4)**
    - Discovered **113** communities
    - Average community size: **10.20** nodes
    - Pairwise accuracy: **0.972**

##### Key Findings:
1. k=4 consistently produces fewer but more tightly-knit communities compared to k=3.
2. Communities exhibit a strong alignment with the in_playlist feature, particularly when k=4 is used.
3. The pairwise accuracy is stable across all three weeks, indicating that the alignment between community structures and the in_playlist feature is consistent over time.
4. The network size remains relatively stable across the three weeks, with minor variations in the number of nodes and edges:
- Week 1: 4,199 nodes, 19,781 edges
- Week 2: 4,172 nodes, 19,742 edges
- Week 3: 4,166 nodes, 19,729 edges

This analysis demonstrates that community structures can reliably reflect whether an artist is included in a playlist. Using a higher value of k (e.g., k=4) leads to more accurate and meaningful community detection results, as the detected communities are smaller and more cohesive. Additionally, the consistency in accuracy across weeks suggests that the network's structural properties remain stable over time.

#### Node Embeddings ML Model

We developed a predictive model using node embeddings to assess an artist's popularity, which was defined by the in_playlist variable. The model incorporated features such as the artist's name, collaborator's name, number of collaborators, artist-collaborator-work count, total published, highest collaboration-work count, popularity, and followers count. We implemented the Node2Vec algorithm, leveraging random walks to generate the node embeddings. We developed three machine learning models to predict popularity: Random Forest, Logistic regression, and Gradient Boosting Classifier. Each model's performance was evaluated using 5-fold cross-validation, yielding the following F1-scores:

##### ML Models with 5-Fold Cross Validation

| ML Model                     | Average F1 Score |
| ---------------------------- | ---------------: |
| Random Forest                |            0.059 |
| Gradient Boosting Classifier |            0.013 |
| Logistic Regression          |            0.000 |

We observed that the F1 scores for all models were notably low. Among them, the Random Forest model emerged as the most effective, achieving an F1 score of 0.059 in predicting artist popularity based on their collaborations. The Gradient Boosting Classifier followed, with an F1 score of 0.013. Further analysis revealed a significant class imbalance in the dataset, which likely impacted the performance of our predictive models

##### ML Models with SMOTE and 5-Fold Cross Validation

| ML Model                     | Average F1 Score |
| ---------------------------- | ---------------: |
| Random Forest                |            0.980 |
| Gradient Boosting Classifier |            0.797 |
| Logistic Regression          |            0.923 |


Upon identifying class imbalance, we implemented the SMOTE technique to enhance our dataset's balance and reassessed the models. As seen in the table above, the improvements were significant: the Random Forest model maintained the highest F1 score (0.980). Notably, the Logistic Regression saw a dramatic improvement, soaring from an F1 score of 0 to 0.797. Similarly, the Gradient Boosting Classifier model's performance increased from 0.013 to 0.923

#### Graph Neural Network
In addition to the ML model, we attempted to make a graph neural network (or GNN). Following the demo we used in our lab, we utilized the Pytorch Geometric Package to create a graph neural network for each week. At first, we attempted the GNN with week one data and used the features of popularity and followers. This was largely unsuccessful, likely due to the class imbalance: there are significantly more artists not included in the Instagram Reels popularity playlist than those who are included. We did a stratified split based on the ‘in_playlist’ column, so that test, train, and validate all had equal proportions of artists in the playlist and not. We were able to get a high accuracy, but a very low F-1 score. Note that the F-1 score is important to analyze here, as it provides a more balanced score than accuracy due to the class imbalance. For example, if a data set has 95% of its labels as ‘False’, you will get an accuracy of 95% if the model spits out ‘False’ for every node.

We attempted to impute some additional features to improve the model. One-hot-encoding the genres was our initial plan, but as there are 1,987 genres this would prove to be computationally challenging. Instead, we imputed the number of genres per artist. Additionally, we calculated the top genres for artists in the playlist and not. The top genres for artists in the playlist are ['pop' 'rap' 'dance-pop' 'pov: indie' 'hip hop'] and for those not in the playlist are  ['covertronica' 'tropical house' 'pop dance' 'rap' 'pop']. We made a column for each of these genres where there is a 1 if the artist is tagged with that genre and 0 otherwise. This was our abbreviated version of one-hot encoding.  We attempted different optimizers, loss functions, hidden layer size, learning rates, and number of epochs to find the best-performing model. Using the torch.nn.CrossEntropyLoss as the optimizer, we got the highest F-1 results.

| Week | Highest Test F1 Score | Highest Test Accuracy Score | Final Loss |
| ---: | --------------------: | --------------------------: | ---------: |
|    1 |                0.2302 |                      0.8302 |     0.5370 |
|    2 |                0.2263 |                      0.8323 |     0.5620 |
|    3 |                0.1769 |                      0.8064 |     0.5770 |


The F-1 Score never exceeded .24, no matter how long we trained or what loss, learning rates, and hidden layers we tried. Due to the limited amount of time for this project, we could not discover a better model.

{{< figure src="https://raw.githubusercontent.com/scmcqueen/SI608NetworksProject/refs/heads/main/graphmodelperformance.png" alt="Loss, Accuracy, and F1 For Week 1 GNN" width="500" >}}

### Challenges
One of the main challenges of the project was securing the data. We designed our own queries to seed the network with the top Instagram Reels song playlist, but we also needed to balance our network with random nodes in order to do the prediction. The Spotify API does not have the functionality to randomly sample different artists and albums. Our initial plan was to download the Spotify Million Playlist Dataset and randomly select artists to include in our graph. Upon inspection, though, we realized that the dataset was created in 2020 and had not been updated since. This seemed like a poor analog for the trending artists on Instagram, as many of them are new artists. For example, the popular EDM artist Ariis who was on the top 100 songs Instagram playlist the first week had not put any music out on Spotify before 2022. To have our random sample representative (in particular, containing newer artists), we instead decided to modify Perry Janssen’s tutorial for querying random songs to get random artists(Janssen 2019). It is not a truly random method, but we believe that it is more representative of the current artists available on Spotify than using an outdated random sample. The music industry moves fast – a lot can change in 6 months, let alone 4 years.

Sadly, we could not resolve all of the challenges and limitations of the Spotify API. The easiest way to query collaborators per artist is to get all of the albums put out by the artist, and then query the list of all artists who contributed to each album. This is convenient, as many albums have unique collaborators on each song, so querying on an album level allows us to get all of the unique collaborators more quickly than querying each song to get collaborators. For example, Brat and It's Completely Different but Also Still Brat has over 20 artists collaborating on it and we can get all of these artists in one query, instead of querying each song in all of Charli XCX’s songs. We are limited to querying only the 50 most recent albums. For a majority of the artists on Spotify, this limit does not impact the data as they have less than 50 albums. There are a few larger artists, though, like Sabrina Carpenter and Harry Styles, with more than 50 albums. We acknowledge that this limits their data and downplays how influential these artists are.

When developing the ML models, the project faced several challenges. One significant issue was the class imbalance in the dataset, as the majority of artists were not part of a trending playlist. This imbalance was addressed using SMOTE, which generated synthetic samples for the minority class, improving model recall without sacrificing precision. Another challenge was the computational complexity associated with calculating network features for thousands of artists, which required optimizing the code and carefully managing resources. Additionally, some features, such as popularity and followers, exhibited high correlation, which necessitated proper scaling and thoughtful feature selection to minimize redundancy and prevent overfitting. These challenges highlighted the need for meticulous preprocessing and optimization to ensure accurate and efficient model development.
The imbalance was also an issue for the graph neural network. The first time we tried to build the GNN, we used the torch.nn.functional.nll_loss() function from the lab GNN demo. This loss function led us to get an F-1 score of 0 consistently. After researching methods for resolving it, we used the torch.nn.CrossEntropyLoss function where we calculate the weights based on the training data, using the sklearn.utils.class_weight.compute_class_weight function. This significantly improved the GNN’s performance.
Conclusions

We tried four different methods for predicting which artists will are in the “Instagram Reels - Top Trending 2024 | NEW VIRAL HIT SONGS ON THE INTERNET” playlist from a network generated with nodes from the playlist, a selection of randomly selected nodes, and their first and second degree collaborators. We wanted to see if using the network collaboration features would be a better way to predict an artist’s success than predicting success based on Spotify features like popularity and followers. Of all the methods we tried, we found that using a random forest machine learning model with node embeddings was the best performing solution (f-1 score of 0.980), compared to a machine learning model without graph information, a machine learning model with graph information (page rank, clustering, etc.), clique percolation community detection, and a graph neural network. This shows that collaboration network information is a helpful feature for predicting which artists will  be popular on Instagram, when combined with other ML features.

In the future, we would recommend that researchers continue to look at the problem of using collaboration networks to predict artist popularity. Perhaps with more time and computing resources, researchers or students would be able to build a more advanced GNN that resulted in a higher F-1 score. In a similar vein, it would be really interesting to look at a network with no limit on the number of collaborators. We noticed that particularly with classical musicians, the limit of 50 albums dampened the number of degrees that the artists should have. With more computational power and greater access to Spotify data, a researcher could analyze a fuller collaboration network to gain more insights.  Additionally, it would be interesting to compare different measures of popularity. With additional time, we would be interested to see how the Instagram popular songs compare to those on TikTok or the Billboard Top 100. Popularity is a hard concept to measure, especially as it differs in different geo-regions. Another interesting area of study would be to compare the popularity of artists and genres in different countries and language markets.

### Citations
Adamic, Lada A; Adar, Eytan. “Friends and neighbors on the Web.” *Social Networks*, vol. 25, no. 
3, 2003, pp. 211-230, https://doi.org/10.1016/S0378-8733(03)00009-1.

Aric A. Hagberg, Daniel A. Schult and Pieter J. Swart, “Exploring network structure, dynamics, and 
function using NetworkX”, in *Proceedings of the 7th Python in Science Conference (SciPy2008)*, 
Gäel Varoquaux, Travis Vaught, and Jarrod Millman (Eds), (Pasadena, CA USA), pp. 11–15, Aug 2008

“Instagram Reels - Top Trending 2024 | NEW VIRAL HIT SONGS ON THE INTERNET.” *Spotify*, 
created by FeelQ Recordings, 30 Sept 2024, 
open.spotify.com/playlist/6dSz0AWxkgBfhlNBFyzqFf.

Janssen, Perry. “Getting random tracks using the Spotify API.” *Medium*, 
https://perryjanssen.medium.com/getting-random-tracks-using-the-spotify-api-61889b0c0c27. 30 
Oct 2024.

Luo, Shijie; Li, He; Huang, Jianbin; Ma, Xiaoke; Cui, Jiangtao; Qiao, Shaojie; Yoo, Jaesoo. 
“Group link prediction in bipartite graphs with graph neural networks.” *Pattern 
Recognition*, vol. 158, 2025, pp. 110977, 
https://www.sciencedirect.com/science/article/pii/S0031320324007283. 

Newman, M. E. J. “The structure of scientific collaboration networks.” *Proceedings of the 
National Academy of Sciences*, vol. 98, no. 2, 2001, pp. 404–409, 
http://dx.doi.org/10.1073/pnas.98.2.404. 

Paszke, A., Gross, S., Massa, F., Lerer, A., Bradbury, J., Chanan, G., … Chintala, S. (2019). *PyTorch: An 
Imperative Style, High-Performance Deep Learning Library.In Advances in Neural Information 
Processing Systems 32* (pp. 8024–8035). Curran Associates, Inc. Retrieved from 
http://papers.neurips.cc/paper/9015-pytorch-an-imperative-style-high-performance-deep-learning-
library.pdf

Pedregosa, Fabian, et al. "Scikit-learn: Machine Learning in Python." *Journal of Machine Learning 
Research*, vol. 12, 2011, pp. 2825-2830.

The pandas development team. pandas-dev/pandas: Pandas. 2020, Zenodo,
doi:10.5281/zenodo.3509134. Accessed 2 Dec. 2024.
