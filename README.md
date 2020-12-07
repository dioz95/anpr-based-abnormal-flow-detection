# anpr-based-abnormal-flow-detection
**This repo is made to showcase my dissertation project in Msc Smart Systems Engineering programme, Newcastle University**

The notebook html shows the detailed data analysis stage to build the automatic abnormal traffic flow detection based on the ANPR cameras data. The camera is positioned in a certain points along the highway within the Newcastle upon Tyne Region. The core algorithm I used in this project is EP-MEANS clustering, that chiefly works like K-MEANS clustering algoritm for the time series data instead of point data. The EP-MEANS algorithm is implemented in R through maotai package. You can look at the documentation written on the references.

### References
1. http://eliassi.org/papers/henderson-sac15.pdf (Research paper on EP-MEANS clustering)
2. https://cran.r-project.org/web/packages/maotai/maotai.pdf (Maotai [R] documentation)
