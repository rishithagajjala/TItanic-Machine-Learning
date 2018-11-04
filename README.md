# TItanic-Machine-Learning
The sinking of the RMS Titanic is one of the most infamous shipwrecks in history.  On April 15, 1912, during her maiden voyage, the Titanic sank after colliding with an iceberg, killing 1502 out of 2224 passengers and crew. This sensational tragedy shocked the international community and led to better safety regulations for ships.  One of the reasons that the shipwreck led to such loss of life was that there were not enough lifeboats for the passengers and crew. Although there was some element of luck involved in surviving the sinking, some groups of people were more likely to survive than others, such as women, children, and the upper-class.


{
 "cells": [
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "## **Table of Contents:**\n",
    "* Introduction\n",
    "* The RMS Titanic\n",
    "* Import Libraries\n",
    "* Getting the Data\n",
    "* Data Exploration/Analysis\n",
    "* Data Preprocessing\n",
    "    - Missing Data\n",
    "    - Converting Features\n",
    "    - Creating Categories\n",
    "    - Creating new Features\n",
    "* Building Machine Learning Models\n",
    "    - Training 8 different models\n",
    "    - Which is the best model ?\n",
    "    - K-Fold Cross Validation\n",
    "* Random Forest \n",
    "    - What is Random Forest ?\n",
    "    - Feature importance\n",
    "    - Hyperparameter Tuning   \n",
    "* Further Evaluation \n",
    "    - Confusion Matrix\n",
    "    - Precision and Recall \n",
    "    - F-Score\n",
    "    - Precision Recall Curve\n",
    "    - ROC AUC Curve\n",
    "    - ROC AUC Score\n",
    "* Submission\n",
    "* Summary"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "### **Introduction**\n",
    "In this kernel I will go through the whole process of creating a machine learning model on the famous Titanic dataset, which is used by many people all over the world. It provides information on the fate of passengers on the Titanic, summarized according to economic status (class), sex, age and survival. In this challenge, we are asked to predict whether a passenger on the titanic would have been survived or not.\n"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "### **The RMS Titanic**\n",
    "RMS Titanic was a British passenger liner that sank in the North Atlantic Ocean in the early morning hours of 15 April 1912, after it collided with an iceberg during its maiden voyage from Southampton to New York City. There were an estimated 2,224 passengers and crew aboard the ship, and more than 1,500 died, making it one of the deadliest commercial peacetime maritime disasters in modern history. The RMS Titanic was the largest ship afloat at the time it entered service and was the second of three Olympic-class ocean liners operated by the White Star Line. The Titanic was built by the Harland and Wolff shipyard in Belfast. Thomas Andrews, her architect, died in the disaster.\n"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "### **Import Libraries**"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 173,
   "metadata": {},
   "outputs": [],
   "source": [
    "# linear algebra\n",
    "import numpy as np \n",
    "\n",
    "# data processing\n",
    "import pandas as pd \n",
    "\n",
    "# data visualization\n",
    "import seaborn as sns\n",
    "%matplotlib inline\n",
    "from matplotlib import pyplot as plt\n",
    "from matplotlib import style\n",
    "\n",
    "# Algorithms\n",
    "from sklearn import linear_model\n",
    "from sklearn.linear_model import LogisticRegression\n",
    "from sklearn.ensemble import RandomForestClassifier\n",
    "from sklearn.linear_model import Perceptron\n",
    "from sklearn.linear_model import SGDClassifier\n",
    "from sklearn.tree import DecisionTreeClassifier\n",
    "from sklearn.neighbors import KNeighborsClassifier\n",
    "from sklearn.svm import SVC, LinearSVC\n",
    "from sklearn.naive_bayes import GaussianNB"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "### **Getting the Data**"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 222,
   "metadata": {},
   "outputs": [],
   "source": [
    "test_df = pd.read_csv(\"test.csv\")\n",
    "train_df = pd.read_csv(\"train.csv\")"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "### **Data Exploration/Analysis**"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 223,
   "metadata": {},
   "outputs": [
    {
     "name": "stdout",
     "output_type": "stream",
     "text": [
      "<class 'pandas.core.frame.DataFrame'>\n",
      "RangeIndex: 891 entries, 0 to 890\n",
      "Data columns (total 12 columns):\n",
      "PassengerId    891 non-null int64\n",
      "Survived       891 non-null int64\n",
      "Pclass         891 non-null int64\n",
      "Name           891 non-null object\n",
      "Sex            891 non-null object\n",
      "Age            714 non-null float64\n",
      "SibSp          891 non-null int64\n",
      "Parch          891 non-null int64\n",
      "Ticket         891 non-null object\n",
      "Fare           891 non-null float64\n",
      "Cabin          204 non-null object\n",
      "Embarked       889 non-null object\n",
      "dtypes: float64(2), int64(5), object(5)\n",
      "memory usage: 83.6+ KB\n"
     ]
    }
   ],
   "source": [
    "train_df.info()"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 224,
   "metadata": {},
   "outputs": [
    {
     "data": {
      "text/html": [
       "<div>\n",
       "<style scoped>\n",
       "    .dataframe tbody tr th:only-of-type {\n",
       "        vertical-align: middle;\n",
       "    }\n",
       "\n",
       "    .dataframe tbody tr th {\n",
       "        vertical-align: top;\n",
       "    }\n",
       "\n",
       "    .dataframe thead th {\n",
       "        text-align: right;\n",
       "    }\n",
       "</style>\n",
       "<table border=\"1\" class=\"dataframe\">\n",
       "  <thead>\n",
       "    <tr style=\"text-align: right;\">\n",
       "      <th></th>\n",
       "      <th>PassengerId</th>\n",
       "      <th>Survived</th>\n",
       "      <th>Pclass</th>\n",
       "      <th>Age</th>\n",
       "      <th>SibSp</th>\n",
       "      <th>Parch</th>\n",
       "      <th>Fare</th>\n",
       "    </tr>\n",
       "  </thead>\n",
       "  <tbody>\n",
       "    <tr>\n",
       "      <th>count</th>\n",
       "      <td>891.000000</td>\n",
       "      <td>891.000000</td>\n",
       "      <td>891.000000</td>\n",
       "      <td>714.000000</td>\n",
       "      <td>891.000000</td>\n",
       "      <td>891.000000</td>\n",
       "      <td>891.000000</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>mean</th>\n",
       "      <td>446.000000</td>\n",
       "      <td>0.383838</td>\n",
       "      <td>2.308642</td>\n",
       "      <td>29.699118</td>\n",
       "      <td>0.523008</td>\n",
       "      <td>0.381594</td>\n",
       "      <td>32.204208</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>std</th>\n",
       "      <td>257.353842</td>\n",
       "      <td>0.486592</td>\n",
       "      <td>0.836071</td>\n",
       "      <td>14.526497</td>\n",
       "      <td>1.102743</td>\n",
       "      <td>0.806057</td>\n",
       "      <td>49.693429</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>min</th>\n",
       "      <td>1.000000</td>\n",
       "      <td>0.000000</td>\n",
       "      <td>1.000000</td>\n",
       "      <td>0.420000</td>\n",
       "      <td>0.000000</td>\n",
       "      <td>0.000000</td>\n",
       "      <td>0.000000</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>25%</th>\n",
       "      <td>223.500000</td>\n",
       "      <td>0.000000</td>\n",
       "      <td>2.000000</td>\n",
       "      <td>20.125000</td>\n",
       "      <td>0.000000</td>\n",
       "      <td>0.000000</td>\n",
       "      <td>7.910400</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>50%</th>\n",
       "      <td>446.000000</td>\n",
       "      <td>0.000000</td>\n",
       "      <td>3.000000</td>\n",
       "      <td>28.000000</td>\n",
       "      <td>0.000000</td>\n",
       "      <td>0.000000</td>\n",
       "      <td>14.454200</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>75%</th>\n",
       "      <td>668.500000</td>\n",
       "      <td>1.000000</td>\n",
       "      <td>3.000000</td>\n",
       "      <td>38.000000</td>\n",
       "      <td>1.000000</td>\n",
       "      <td>0.000000</td>\n",
       "      <td>31.000000</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>max</th>\n",
       "      <td>891.000000</td>\n",
       "      <td>1.000000</td>\n",
       "      <td>3.000000</td>\n",
       "      <td>80.000000</td>\n",
       "      <td>8.000000</td>\n",
       "      <td>6.000000</td>\n",
       "      <td>512.329200</td>\n",
       "    </tr>\n",
       "  </tbody>\n",
       "</table>\n",
       "</div>"
      ],
      "text/plain": [
       "       PassengerId    Survived      Pclass         Age       SibSp  \\\n",
       "count   891.000000  891.000000  891.000000  714.000000  891.000000   \n",
       "mean    446.000000    0.383838    2.308642   29.699118    0.523008   \n",
       "std     257.353842    0.486592    0.836071   14.526497    1.102743   \n",
       "min       1.000000    0.000000    1.000000    0.420000    0.000000   \n",
       "25%     223.500000    0.000000    2.000000   20.125000    0.000000   \n",
       "50%     446.000000    0.000000    3.000000   28.000000    0.000000   \n",
       "75%     668.500000    1.000000    3.000000   38.000000    1.000000   \n",
       "max     891.000000    1.000000    3.000000   80.000000    8.000000   \n",
       "\n",
       "            Parch        Fare  \n",
       "count  891.000000  891.000000  \n",
       "mean     0.381594   32.204208  \n",
       "std      0.806057   49.693429  \n",
       "min      0.000000    0.000000  \n",
       "25%      0.000000    7.910400  \n",
       "50%      0.000000   14.454200  \n",
       "75%      0.000000   31.000000  \n",
       "max      6.000000  512.329200  "
      ]
     },
     "execution_count": 224,
     "metadata": {},
     "output_type": "execute_result"
    }
   ],
   "source": [
    "train_df.describe()"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "Above we can see that **38% out of the training-set survived the Titanic**. We can also see that the passenger ages range from 0.4 to 80. On top of that we can already detect some features, that contain missing values, like the 'Age' feature."
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 225,
   "metadata": {},
   "outputs": [
    {
     "data": {
      "text/html": [
       "<div>\n",
       "<style scoped>\n",
       "    .dataframe tbody tr th:only-of-type {\n",
       "        vertical-align: middle;\n",
       "    }\n",
       "\n",
       "    .dataframe tbody tr th {\n",
       "        vertical-align: top;\n",
       "    }\n",
       "\n",
       "    .dataframe thead th {\n",
       "        text-align: right;\n",
       "    }\n",
       "</style>\n",
       "<table border=\"1\" class=\"dataframe\">\n",
       "  <thead>\n",
       "    <tr style=\"text-align: right;\">\n",
       "      <th></th>\n",
       "      <th>PassengerId</th>\n",
       "      <th>Survived</th>\n",
       "      <th>Pclass</th>\n",
       "      <th>Name</th>\n",
       "      <th>Sex</th>\n",
       "      <th>Age</th>\n",
       "      <th>SibSp</th>\n",
       "      <th>Parch</th>\n",
       "      <th>Ticket</th>\n",
       "      <th>Fare</th>\n",
       "      <th>Cabin</th>\n",
       "      <th>Embarked</th>\n",
       "    </tr>\n",
       "  </thead>\n",
       "  <tbody>\n",
       "    <tr>\n",
       "      <th>0</th>\n",
       "      <td>1</td>\n",
       "      <td>0</td>\n",
       "      <td>3</td>\n",
       "      <td>Braund, Mr. Owen Harris</td>\n",
       "      <td>male</td>\n",
       "      <td>22.0</td>\n",
       "      <td>1</td>\n",
       "      <td>0</td>\n",
       "      <td>A/5 21171</td>\n",
       "      <td>7.2500</td>\n",
       "      <td>NaN</td>\n",
       "      <td>S</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>1</th>\n",
       "      <td>2</td>\n",
       "      <td>1</td>\n",
       "      <td>1</td>\n",
       "      <td>Cumings, Mrs. John Bradley (Florence Briggs Th...</td>\n",
       "      <td>female</td>\n",
       "      <td>38.0</td>\n",
       "      <td>1</td>\n",
       "      <td>0</td>\n",
       "      <td>PC 17599</td>\n",
       "      <td>71.2833</td>\n",
       "      <td>C85</td>\n",
       "      <td>C</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>2</th>\n",
       "      <td>3</td>\n",
       "      <td>1</td>\n",
       "      <td>3</td>\n",
       "      <td>Heikkinen, Miss. Laina</td>\n",
       "      <td>female</td>\n",
       "      <td>26.0</td>\n",
       "      <td>0</td>\n",
       "      <td>0</td>\n",
       "      <td>STON/O2. 3101282</td>\n",
       "      <td>7.9250</td>\n",
       "      <td>NaN</td>\n",
       "      <td>S</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>3</th>\n",
       "      <td>4</td>\n",
       "      <td>1</td>\n",
       "      <td>1</td>\n",
       "      <td>Futrelle, Mrs. Jacques Heath (Lily May Peel)</td>\n",
       "      <td>female</td>\n",
       "      <td>35.0</td>\n",
       "      <td>1</td>\n",
       "      <td>0</td>\n",
       "      <td>113803</td>\n",
       "      <td>53.1000</td>\n",
       "      <td>C123</td>\n",
       "      <td>S</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>4</th>\n",
       "      <td>5</td>\n",
       "      <td>0</td>\n",
       "      <td>3</td>\n",
       "      <td>Allen, Mr. William Henry</td>\n",
       "      <td>male</td>\n",
       "      <td>35.0</td>\n",
       "      <td>0</td>\n",
       "      <td>0</td>\n",
       "      <td>373450</td>\n",
       "      <td>8.0500</td>\n",
       "      <td>NaN</td>\n",
       "      <td>S</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>5</th>\n",
       "      <td>6</td>\n",
       "      <td>0</td>\n",
       "      <td>3</td>\n",
       "      <td>Moran, Mr. James</td>\n",
       "      <td>male</td>\n",
       "      <td>NaN</td>\n",
       "      <td>0</td>\n",
       "      <td>0</td>\n",
       "      <td>330877</td>\n",
       "      <td>8.4583</td>\n",
       "      <td>NaN</td>\n",
       "      <td>Q</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>6</th>\n",
       "      <td>7</td>\n",
       "      <td>0</td>\n",
       "      <td>1</td>\n",
       "      <td>McCarthy, Mr. Timothy J</td>\n",
       "      <td>male</td>\n",
       "      <td>54.0</td>\n",
       "      <td>0</td>\n",
       "      <td>0</td>\n",
       "      <td>17463</td>\n",
       "      <td>51.8625</td>\n",
       "      <td>E46</td>\n",
       "      <td>S</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>7</th>\n",
       "      <td>8</td>\n",
       "      <td>0</td>\n",
       "      <td>3</td>\n",
       "      <td>Palsson, Master. Gosta Leonard</td>\n",
       "      <td>male</td>\n",
       "      <td>2.0</td>\n",
       "      <td>3</td>\n",
       "      <td>1</td>\n",
       "      <td>349909</td>\n",
       "      <td>21.0750</td>\n",
       "      <td>NaN</td>\n",
       "      <td>S</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>8</th>\n",
       "      <td>9</td>\n",
       "      <td>1</td>\n",
       "      <td>3</td>\n",
       "      <td>Johnson, Mrs. Oscar W (Elisabeth Vilhelmina Berg)</td>\n",
       "      <td>female</td>\n",
       "      <td>27.0</td>\n",
       "      <td>0</td>\n",
       "      <td>2</td>\n",
       "      <td>347742</td>\n",
       "      <td>11.1333</td>\n",
       "      <td>NaN</td>\n",
       "      <td>S</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>9</th>\n",
       "      <td>10</td>\n",
       "      <td>1</td>\n",
       "      <td>2</td>\n",
       "      <td>Nasser, Mrs. Nicholas (Adele Achem)</td>\n",
       "      <td>female</td>\n",
       "      <td>14.0</td>\n",
       "      <td>1</td>\n",
       "      <td>0</td>\n",
       "      <td>237736</td>\n",
       "      <td>30.0708</td>\n",
       "      <td>NaN</td>\n",
       "      <td>C</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>10</th>\n",
       "      <td>11</td>\n",
       "      <td>1</td>\n",
       "      <td>3</td>\n",
       "      <td>Sandstrom, Miss. Marguerite Rut</td>\n",
       "      <td>female</td>\n",
       "      <td>4.0</td>\n",
       "      <td>1</td>\n",
       "      <td>1</td>\n",
       "      <td>PP 9549</td>\n",
       "      <td>16.7000</td>\n",
       "      <td>G6</td>\n",
       "      <td>S</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>11</th>\n",
       "      <td>12</td>\n",
       "      <td>1</td>\n",
       "      <td>1</td>\n",
       "      <td>Bonnell, Miss. Elizabeth</td>\n",
       "      <td>female</td>\n",
       "      <td>58.0</td>\n",
       "      <td>0</td>\n",
       "      <td>0</td>\n",
       "      <td>113783</td>\n",
       "      <td>26.5500</td>\n",
       "      <td>C103</td>\n",
       "      <td>S</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>12</th>\n",
       "      <td>13</td>\n",
       "      <td>0</td>\n",
       "      <td>3</td>\n",
       "      <td>Saundercock, Mr. William Henry</td>\n",
       "      <td>male</td>\n",
       "      <td>20.0</td>\n",
       "      <td>0</td>\n",
       "      <td>0</td>\n",
       "      <td>A/5. 2151</td>\n",
       "      <td>8.0500</td>\n",
       "      <td>NaN</td>\n",
       "      <td>S</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>13</th>\n",
       "      <td>14</td>\n",
       "      <td>0</td>\n",
       "      <td>3</td>\n",
       "      <td>Andersson, Mr. Anders Johan</td>\n",
       "      <td>male</td>\n",
       "      <td>39.0</td>\n",
       "      <td>1</td>\n",
       "      <td>5</td>\n",
       "      <td>347082</td>\n",
       "      <td>31.2750</td>\n",
       "      <td>NaN</td>\n",
       "      <td>S</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>14</th>\n",
       "      <td>15</td>\n",
       "      <td>0</td>\n",
       "      <td>3</td>\n",
       "      <td>Vestrom, Miss. Hulda Amanda Adolfina</td>\n",
       "      <td>female</td>\n",
       "      <td>14.0</td>\n",
       "      <td>0</td>\n",
       "      <td>0</td>\n",
       "      <td>350406</td>\n",
       "      <td>7.8542</td>\n",
       "      <td>NaN</td>\n",
       "      <td>S</td>\n",
       "    </tr>\n",
       "  </tbody>\n",
       "</table>\n",
       "</div>"
      ],
      "text/plain": [
       "    PassengerId  Survived  Pclass  \\\n",
       "0             1         0       3   \n",
       "1             2         1       1   \n",
       "2             3         1       3   \n",
       "3             4         1       1   \n",
       "4             5         0       3   \n",
       "5             6         0       3   \n",
       "6             7         0       1   \n",
       "7             8         0       3   \n",
       "8             9         1       3   \n",
       "9            10         1       2   \n",
       "10           11         1       3   \n",
       "11           12         1       1   \n",
       "12           13         0       3   \n",
       "13           14         0       3   \n",
       "14           15         0       3   \n",
       "\n",
       "                                                 Name     Sex   Age  SibSp  \\\n",
       "0                             Braund, Mr. Owen Harris    male  22.0      1   \n",
       "1   Cumings, Mrs. John Bradley (Florence Briggs Th...  female  38.0      1   \n",
       "2                              Heikkinen, Miss. Laina  female  26.0      0   \n",
       "3        Futrelle, Mrs. Jacques Heath (Lily May Peel)  female  35.0      1   \n",
       "4                            Allen, Mr. William Henry    male  35.0      0   \n",
       "5                                    Moran, Mr. James    male   NaN      0   \n",
       "6                             McCarthy, Mr. Timothy J    male  54.0      0   \n",
       "7                      Palsson, Master. Gosta Leonard    male   2.0      3   \n",
       "8   Johnson, Mrs. Oscar W (Elisabeth Vilhelmina Berg)  female  27.0      0   \n",
       "9                 Nasser, Mrs. Nicholas (Adele Achem)  female  14.0      1   \n",
       "10                    Sandstrom, Miss. Marguerite Rut  female   4.0      1   \n",
       "11                           Bonnell, Miss. Elizabeth  female  58.0      0   \n",
       "12                     Saundercock, Mr. William Henry    male  20.0      0   \n",
       "13                        Andersson, Mr. Anders Johan    male  39.0      1   \n",
       "14               Vestrom, Miss. Hulda Amanda Adolfina  female  14.0      0   \n",
       "\n",
       "    Parch            Ticket     Fare Cabin Embarked  \n",
       "0       0         A/5 21171   7.2500   NaN        S  \n",
       "1       0          PC 17599  71.2833   C85        C  \n",
       "2       0  STON/O2. 3101282   7.9250   NaN        S  \n",
       "3       0            113803  53.1000  C123        S  \n",
       "4       0            373450   8.0500   NaN        S  \n",
       "5       0            330877   8.4583   NaN        Q  \n",
       "6       0             17463  51.8625   E46        S  \n",
       "7       1            349909  21.0750   NaN        S  \n",
       "8       2            347742  11.1333   NaN        S  \n",
       "9       0            237736  30.0708   NaN        C  \n",
       "10      1           PP 9549  16.7000    G6        S  \n",
       "11      0            113783  26.5500  C103        S  \n",
       "12      0         A/5. 2151   8.0500   NaN        S  \n",
       "13      5            347082  31.2750   NaN        S  \n",
       "14      0            350406   7.8542   NaN        S  "
      ]
     },
     "execution_count": 225,
     "metadata": {},
     "output_type": "execute_result"
    }
   ],
   "source": [
    "train_df.head(15)"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "From the table above, we can note a few things. First of all, that we **need to convert a lot of features into numeric** ones later on, so that the machine learning algorithms can process them. Furthermore, we can see that the **features have widely different ranges**, that we will need to convert into roughly the same scale. We can also spot some more features, that contain missing values (NaN = not a number), that wee need to deal with.\n",
    "\n",
    "**Let's take a more detailed look at what data is actually missing:**"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 226,
   "metadata": {},
   "outputs": [
    {
     "data": {
      "text/html": [
       "<div>\n",
       "<style scoped>\n",
       "    .dataframe tbody tr th:only-of-type {\n",
       "        vertical-align: middle;\n",
       "    }\n",
       "\n",
       "    .dataframe tbody tr th {\n",
       "        vertical-align: top;\n",
       "    }\n",
       "\n",
       "    .dataframe thead th {\n",
       "        text-align: right;\n",
       "    }\n",
       "</style>\n",
       "<table border=\"1\" class=\"dataframe\">\n",
       "  <thead>\n",
       "    <tr style=\"text-align: right;\">\n",
       "      <th></th>\n",
       "      <th>Total</th>\n",
       "      <th>%</th>\n",
       "    </tr>\n",
       "  </thead>\n",
       "  <tbody>\n",
       "    <tr>\n",
       "      <th>Cabin</th>\n",
       "      <td>687</td>\n",
       "      <td>77.1</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>Age</th>\n",
       "      <td>177</td>\n",
       "      <td>19.9</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>Embarked</th>\n",
       "      <td>2</td>\n",
       "      <td>0.2</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>Fare</th>\n",
       "      <td>0</td>\n",
       "      <td>0.0</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>Ticket</th>\n",
       "      <td>0</td>\n",
       "      <td>0.0</td>\n",
       "    </tr>\n",
       "  </tbody>\n",
       "</table>\n",
       "</div>"
      ],
      "text/plain": [
       "          Total     %\n",
       "Cabin       687  77.1\n",
       "Age         177  19.9\n",
       "Embarked      2   0.2\n",
       "Fare          0   0.0\n",
       "Ticket        0   0.0"
      ]
     },
     "execution_count": 226,
     "metadata": {},
     "output_type": "execute_result"
    }
   ],
   "source": [
    "total= train_df.isnull().sum().sort_values(ascending= False)\n",
    "percent_1= train_df.isnull().sum()/train_df.isnull().count()*100\n",
    "percent_2 = (round(percent_1, 1)).sort_values(ascending=False)\n",
    "missing_data = pd.concat([total, percent_2], axis=1, keys=['Total', '%'])\n",
    "missing_data.head(5)"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "The Embarked feature has only 2 missing values, which can easily be filled. It will be much more tricky, to deal with the 'Age' feature, which has 177 missing values. The 'Cabin' feature needs further investigation, but it looks like that we might want to drop it from the dataset, since 77 % of it are missing."
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 227,
   "metadata": {},
   "outputs": [
    {
     "data": {
      "text/plain": [
       "array(['PassengerId', 'Survived', 'Pclass', 'Name', 'Sex', 'Age', 'SibSp',\n",
       "       'Parch', 'Ticket', 'Fare', 'Cabin', 'Embarked'], dtype=object)"
      ]
     },
     "execution_count": 227,
     "metadata": {},
     "output_type": "execute_result"
    }
   ],
   "source": [
    "train_df.columns.values"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "Above you can see the 11 features + the target variable (survived). **What features could contribute to a high survival rate ?** \n",
    "\n",
    "To me it would make sense if everything except 'PassengerId', 'Ticket' and 'Name'  would be correlated with a high survival rate. "
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "**1. Age and Sex:**"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 228,
   "metadata": {},
   "outputs": [
    {
     "data": {
      "image/png": "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2jRee+01Hn/88e2Wf/PNNwH4xS9+wfz58wG4+OKLueqqq2r1o0rVa53R8fyWj/dtHcqWPbt6hmFJ2znssMNoa2vjgQce4Oqrr2bixIn0799/22myHb++Zo899tj2+IADDmDfffdl8eLFzJ07lxtvvHGn9U+aNImrr76al19+mba2Nk4++WQ2btzIkCFDWLRoUYc1+WltSeqYPbt6XiYhaTtr1qzhne98JxdddBFXXnklCxcupLm5mba2NoBt/+PvzOTJk/n617/Ohg0bGD169E7PDx48mHHjxjF9+nTOOuss+vXrx1577cWIESOYN28eUPoS9aeeegqA448/njlz5gAwa9asWv6oktTw7NnVMwxL2s7TTz/NuHHjGDNmDF/96lf5q7/6K7785S8zffp0TjjhBPr167fL159zzjnMmTOHj33sY50uc95553H77bdz3nnnbZs3a9YsbrnlFo466iiOOOII7rvvPgC+/e1v853vfIdjjjmGDRs21OaHlKTdhD27epFS6rONtbS0pNbW1j7bnnrGa4brZ9myZYwcObLeZWSvo99DRLSllFrqVFJd2LMr4DXDWbNnF0c1fdsjw5IkScqWYViSJEnZMgxLkiQpW4ZhSZIkZcvvGZYkqdY6+2Ad+OE6qWA8MixJkqRsGYYlVWXmzJmsWbOmT7f5/ve/vybrufTSS7nrrrtqsi5JagT27J15mYRUZLs61doTvXB6dubMmYwaNYphw4bVbJ0pJVJKvO1tHf9//fHHH6/ZtiSpZuzZHT5f9J7tkWFJ26xcuZKRI0fyyU9+kiOOOIKJEyfyhz/8AYBFixZx7LHHcuSRR3L22Wfzu9/9jrvuuovW1lYuvPBCxowZs23Zrf7hH/6Bww8/nCOPPJLJkycDcM0113D99ddvW2bUqFGsXLly27anTZvG0UcfzVe+8hW++MUvbltu5syZfOYznwFKw4NCaVSkBx54YNsyl156KfPnz2fz5s184Qtf4JhjjuHII4/kxhtvBEoN+4orruDwww/nzDPPZN26db3wLkpS37Bn14ZhWNJ2nn32WS6//HKWLl3KkCFDto1rP2XKFL72ta+xePFiRo8ezbXXXss555xDS0sLs2bNYtGiRQwaNGi7dV133XU8+eSTLF68mH/6p3/qctvLly9nypQpPPnkk0ybNo27775723Nz587dbihQgMmT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\n",
      "text/plain": [
       "<matplotlib.figure.Figure at 0x20c83935748>"
      ]
     },
     "metadata": {},
     "output_type": "display_data"
    }
   ],
   "source": [
    "survived = 'survived'\n",
    "not_survived = 'not survived'\n",
    "fig, axes = plt.subplots(nrows=1, ncols=2,figsize=(12, 5))\n",
    "women = train_df[train_df['Sex']=='female']\n",
    "men = train_df[train_df['Sex']=='male']\n",
    "ax = sns.distplot(women[women['Survived']==1].Age.dropna(), bins=18, label = survived, ax = axes[0], kde =False)\n",
    "ax = sns.distplot(women[women['Survived']==0].Age.dropna(), bins=40, label = not_survived, ax = axes[0], kde =False)\n",
    "ax.legend()\n",
    "ax.set_title('Female')\n",
    "ax = sns.distplot(men[men['Survived']==1].Age.dropna(), bins=18, label = survived, ax = axes[1], kde = False)\n",
    "ax = sns.distplot(men[men['Survived']==0].Age.dropna(), bins=40, label = not_survived, ax = axes[1], kde = False)\n",
    "ax.legend()\n",
    "_= ax.set_title('Male')\n"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "You can see that men have a high probability of survival when they are between 18 and 30 years old, which is also a little bit true for women but not fully. For women the survival chances are higher between 14 and 40.\n",
    "\n",
    "For men the probability of survival is very low between the age of 5 and 18, but that isn't true for women. Another thing to note is that infants also have a little bit higher probability of survival.\n",
    "\n",
    "Since there seem to be **certain ages, which have increased odds of survival** and because I want every feature to be roughly on the same scale, I will create age groups later on."
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "**3. Embarked, Pclass  and Sex:**"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 229,
   "metadata": {},
   "outputs": [
    {
     "data": {
      "text/plain": [
       "<seaborn.axisgrid.FacetGrid at 0x20c8392a128>"
      ]
     },
     "execution_count": 229,
     "metadata": {},
     "output_type": "execute_result"
    },
    {
     "data": {
      "image/png": "iVBORw0KGgoAAAANSUhEUgAAAkUAAAPECAYAAACg5U8IAAAABHNCSVQICAgIfAhkiAAAAAlwSFlzAAALEgAACxIB0t1+/AAAADl0RVh0U29mdHdhcmUAbWF0cGxvdGxpYiB2ZXJzaW9uIDIuMS4yLCBodHRwOi8vbWF0cGxvdGxpYi5vcmcvNQv5yAAAIABJREFUeJzs3Xl8VNX9//HXJ/vKTlEMCCio7GBYRQVBmdRvtUUUwbovVWtt7aK10n6/tX791mpbq1UQFUW0KEq1/tQEAUWQTQICAiIgIEQEgUCA7Mv5/ZEQQxKSScjkZibv5+Mxj8nce+6dz/gI4zvnnnOuOecQERERae7CvC5AREREpClQKBIRERFBoUhEREQEUCgSERERARSKRERERACFIhERERFAoUgkaJlZsZmtqfD4bR2OHWlm75zk+y80s+R6HnvS7192nv8ys0/NbK2ZbTSzn5zsOUWk+YrwugARqbdc51x/L97YzMK9eN9KNUQC04DBzrkMM4sGunhblYgEM/UUiYQYM9thZg+b2TIzSzezgWY218y+NLPbKzRtYWZvlvWwTDWzsLLjp5Qdt8HM/ljpvH8ws4+BKytsDzOzGWb2UNnrS8ree7WZvW5mCWXbfWa2qez4cQ3wURMp/cPuAIBzLt8590UDnFdEmimFIpHgFVvp8tmECvt2OeeGAYuBF4HxwFDgwQptBgO/AvoAZ/BdUHnAOZcM9AUuNLO+FY7Jc86NcM69WvY6AngF2Oycm2xm7YDJwBjn3EAgHfilmcUAzwI/AM4HTqnuA5nZWZU+U8VHq4ptnXOZwNvAV2Y2y8yuORbsRETqQ5fPRIJXTZfP3i57/gxIcM4dAY6YWV6FcPGJc24bgJnNAkYAbwBXmdltlH4/nAr0BNaVHfNapfd5BpjtnPvfstdDy9ovMTOAKGAZcDaw3Tm3pez9XgZuq1x0WU+P35cEnXO3mFkfYAzwa+Bi4AZ/jxcRqUihSCQ05Zc9l1T4+djrY//uK9/40JlZV0rDxSDn3EEzexGIqdAmu9IxS4FRZvZX51weYMA859zEio3MrH8171eFmZ1F1eB1zEjn3KHKG51znwGfmdlMYDsKRSJST+pqFmm+BptZ17JLThOAj4EWlAafLDPrAKTUco7ngfeA180sAlgOnGdmZwKYWZyZ9QA2AV3N7Iyy4yZWdzLn3BfOuf4neBwXiMwswcxGVtjUH/iqDp9fROQ46ikSCV6xZramwus055zf0/Ipvaz1Z0rHFC0C3nTOlZjZp8AGYBuwpLaTOOf+ZmYtgZnANZT21Mwqmw0GMNk5t7nskty7Zraf0gDWuw61VseAe83sGSCX0jB3w0meU0SaMXOu1h5tERERkZCny2ciIiIiKBSJiIiIAApFIiIiIoBCkYiIiAgQhLPPfD6fS0tL87oMERGRpsq8LiBYBV1P0f79+70uQUREREJQ0IUiERERkUBQKBIRERFBoUhEREQEUCgSERERARSKRERERACFIhERERFAoUhEREQEUCgSERERARSKRERERACFIhERERFAoUhEREQECGAoMrPpZvatma0/wX4zsyfMbKuZrTOzgYGqRURERKQ2gewpehHw1bA/Behe9rgNmBLAWkRERERqFBGoEzvnFplZlxqaXA685JxzwHIza2VmpzrnvglUTc3Ntc+vIONgLkmtY5l58xCvyxEREWnSAhaK/HAasKvC64yybVVCkZndRmlvEp07d26U4kJBxsFctu/P9roMERGRoODlQGurZpurrqFzbppzLtk5l9y+ffsAlyUiIiLNkZehKAPoVOF1ErDbo1pERESkmfMyFL0NXFc2C20okKXxRCIiIuKVgI0pMrNZwEignZllAP8NRAI456YC7wHfB7YCOcCNgapFREREpDaBnH02sZb9DvhpoN5fREREpC60onWIyiss5mh+EQDZ+UUUFJV4XJGIiEjTplAUguZt3Muw/1vAviP5AHx7JJ/zHvmApVv3e1yZhJprn1/BqMcWcu3zK7wuRUTkpCkUhZj1X2dxx8urOJhTeNz2fUfyuXlGOtv2HfWoMglFx9bCyjiY63UpIiInTaEoxExbtI2ikmqXeyK3sJinPtyqS2kiIiLV8HJFawmA5dsO1Lh/zuqvmbP6axKjI2gdH0Xr+CjaxEWWPUfRJqH0uXV8FG3io2gdV/rcMjaS8LDq1tsUEREJDQpFIcbf4HIkv4gj+UXszMzxq32YQau4KFrHRR4Xlo6FqdbxUbQ97nUkCdERmClIiYhIcFAoCjEjz2rPrE92nXD/2ack0qlNHAezC8jMKeBgdgGHcgtx1V9xK1fiIDO7gMzsAr7c59/91CLD7bvwVLkX6ljvVIWA1SY+ipjI8Lp8XBERkQajUBRi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5AwUikREaphWrVoxY8YM3nnnHXr16gUU7zeaOXOm+o1ESqFQJCJSQ0VHR/P+++/z2GOP0axZMyC/3+jRRx9l1KhRLFy4UP1GIkUoFImI1GAej4df/OIXLF68mNtuu42goCAgv9/oN7/5Dddddx0bN270c5UiVYNCkYhILRASEsJdd93FokWLGDt2bOH2k/1GU6dOVb+R1HoKRSIitUirVq14+umni/UbOed45513iImJ4fnnn1e/kdRaCkUiIrXQyX6jxx9/nObNmwP5/UaPPfaY+o2k1lIoEhGppTweDz//+c9ZtGgRt99++yn9Rtdeey0bNmzwc5UilUehSESklgsJCeH3v/89ixcvLtZvtGrVKsaNG8f999+vfiOpFRSKREQEgJYtW/L0008zZ84cevfuDeT3G82ZM0f9RlIrKBSJiEgxAwYM4L333uOJJ544pd/opz/9qfqNpMZSKBIRkVN4PB4uv/xyFi9ezG9/+9vCfqNdu3YV9hutX7/ez1WKVCyFIhEROa169erxu9/9jsWLFzNu3LjC7atWrWL8+PHcf//9pKSk+LFCkYqjUCQiImfVsmVLnnrqKd5991369OkD/NBvNHLkSPUbSY2gUCQiImXWv39/5s6dy/Tp02nRogVQvN/o448/Vr+RVFsKRSIi8qN4PB4uu+wyFi1axB133EFwcDCQ32902223MXHiRPUbSbWkUCQiIuVSr1497rzzThYtWsT48eMLt3/99deMHz+e++67T/1GUq0oFImIyDlp2bIlTz75JHPnzqVv375Afr/Ru+++S0xMDP/617/UbyTVgkKRiIhUiH79+vHuu+/y5JNPFvYbZWRk8MQTT3DJJZfw3//+V/1GUqUpFImISIXxeDyMHz+eRYsWceeddxb2GyUlJXH77bczYcIE1q1b5+cqRUqnUCQiIhWuXr163HHHHSxevLhYv9Hq1au57LLLuPfee9m/f78fKxQ5lUKRiIj4TERERKn9RnPnzmXkyJHqN5IqRaFIRER87mS/0VNPPVVqv9GCBQvUbyR+p1AkIiKVwuPxMG7cOBYvXnxKv9Fvf/tbrrnmGtauXVu4/86dO0lPTwcgOzvbLzVL7WLVLZlHR0e72NhYf5chIiLnKDk5mSeeeIIPP/ywcJuZcdlll5GZmXnK1WqjR4/mscceo169ev4otzoxfxdQXSkUiYiI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8XgIDg52Jx/n5OQYwJQpU6Lmzp0b/5Of/OT4jBkzmn722WehRY+Tm5tLaGhozqZNmzaUp76yrlP0pJltMrO/mFmP8ryRiIiIVE1XDohMPdP4FQNal6ufqKThw4enTZ8+vXleXn4b0MqVK+v+mNcfO3bM06ZNm+zMzEx7++23m5Qcb9KkSV5kZGTWyy+/3BggLy+PL7/8sszvUdZ1ioYDw4AUYKaZrTWzB8v6JiIiIlJ1XTOwzcGLuzc/WNrY0M7hh264oO2BinifadOm7cnJybGuXbt279SpU48HH3yw1Y95/X333bdn4MCB3YYMGdK5U6dOpTZPv/XWWwmzZs0K69KlS/dOnTr1eO+990ptIC+NOffjrrAzs17AH4GrnXNnPAfpC9HR0S42Vi1OZTH8iWUkpmYQFRbC0ruH+bscqYH0GRNfi4mJYfv27bRr144lS5b4u5zqolgPzZo1a7b36dPnjDNBALl5jndW72zybmxS2N60E4HNGgRn/aJ/q9SJg9ocDPDUvPvIrlmzJqxPnz7tim4r6zpF3chfuPEK4ADwNvCHii5QRERE/MPrMSYOantw4qC2pc4Y1QZlXadoFvAWcIlzbo8P6xERERHxi7OGIjPzAtucc09XQj0iIiIifnHWk4TOuVygqZlVev+QiIiISGUp6+mzHcBKM5sHFN73zDn3pE+qEhEREalkZQ1Fewq+PEDoWfYVERERqXbKFIqcc3/2dSEiIiLiP9nZ2cyePTvs/fffD9+/f39geHh41mWXXZZ68803pxZdbboyffTRR6HTp09vvnTp0vjKeL+yXpK/FDjlG+KcG1HhFYmIiEilys7OZtKkSR1WrlxZuNDh4cOHAx5//PE2n332WcNXXnllm7+CUWUq62pMdwP3FHz9D/A9ukmsiIhIjfDmm282LRqIivr6668bzpo165S70ZfV5s2bA6OionpcffXVbTt16tRj3LhxUR9++GFo//79u7Zt27bn0qVL6y1durRev379unbr1q17v379uq5Zsyao5HHS0tI8V155ZbuePXt269atW/fXX3+9zCtVl1VZb/PxTZGvlc65u4BBFV2MiIiIVL7333//jKHnww8/LHcoAti1a1fwH/7wh/2bNm1av23btuA33nijaWxs7Ka//vWvSX/9618j+vTpc+Lrr7/etHHjxg3/+7//u/uPf/xjZMlj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IiIiAigUiYiIiAAKRSIiIiKAQpGIiIgIoFAkIiIiAigUiYiIiAAQ4O8CRERETieycd1if4r4kkKRiIhUWa9NGuTvEqQW0ekzERERERSKRERERACFIhERERFAoUhEREQEUCgSERERARSKRERERACFIhERERFAoUhEREQEUCgSERERARSKRERERACFIhERERFAoUhEREQEUCgSERERARSKRERERACFIhERERFAoUhEREQEUCgSERERARSKRERERACFIhERERFAoUhEREQEUCgSERERARSKRERERACFIhERERHAx6HIzEaZ2WYzizez+0oZDzKzdwrGV5lZO1/WIyIiInI6PgtFZuYFngVGA92BCWbWvcRuk4BDzrmOwFPAo76qR0RERORMfDlTNBCId84lOOeygLeB8SX2GQ+8UvB4LhBjZubDmkRERERK5ctQ1ArYVeR5UsG2UvdxzuUAR4CmPqxJREREpFS+DEWlzfi4cuyDmU0xs1gzi01JSamQ4kRERESK8mUoSgJaF3keCew53T5mFgA0BA6WPJBzbqZzLto5Fx0eHu6jckXkx4psXJeosBAiG9f1dykiIucswIfHXg10MrMoYDdwDTCxxD7zgBuAL4ErgE+dc6fMFIlI1fTapEH+LkFEpML4LBQ553LM7HZgIeAFXnbOrTezh4FY59w84CXgNTOLJ3+G6Bpf1SMiIiJyJlbdJmaio6NdbGysv8sQERGpqnQVdzlpRWsRERERFIpEREREAIUiEREREUChSERERARQKBIREREBFIpEREREAIUiEREREUChSERERARQKBIREREBFIpEREREAIUiEREREaAa3vvMzFKAHf6uoxoJA1L9XYTUaPqMia/pM/bjpDrnRvm7iOqo2oUi+XHMLNY5F+3vOqTm0mdMfE2fMaksOn0mIiIigkKRiIiICKBQVBvM9HcBUuPpMya+ps+YVAr1FImIiIigmSIRERERQKFIREREBFAoEhEREQEUikREREQAhSIRERERQKFIREREBFAoEhEREQEUikREREQAhSIRERERQKFIpFoys1wz+77I130/4rXDzOyjc3z/ZWZWrruWV8T7Fxwn0Mz+YWbbzCzezD4yszbnelwRqb0C/F2AiJTLcedcX3+8sZl5/fG+pfgbEAp0ds7lmtlNwL/NbIBzLs/PtYlINaSZIpEaxMy2m9nf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\n",
      "text/plain": [
       "<matplotlib.figure.Figure at 0x20c8392a358>"
      ]
     },
     "metadata": {},
     "output_type": "display_data"
    }
   ],
   "source": [
    "FacetGrid = sns.FacetGrid(train_df, row='Embarked', size=4.5, aspect=1.6)\n",
    "FacetGrid.map(sns.pointplot, 'Pclass', 'Survived', 'Sex', palette=None,  order=None, hue_order=None )\n",
    "FacetGrid.add_legend()"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "Embarked seems to be correlated with survival, depending on the gender. \n",
    "\n",
    "Women on port Q and on port S have a higher chance of survival. The inverse is true, if they are at port C. Men have a high survival probability if they are on port C, but a low probability if they are on port Q or S. \n",
    "\n",
    "Pclass also seems to be correlated with survival. We will generate another plot of it below."
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 230,
   "metadata": {},
   "outputs": [
    {
     "data": {
      "text/plain": [
       "<matplotlib.axes._subplots.AxesSubplot at 0x20c82b4bb00>"
      ]
     },
     "execution_count": 230,
     "metadata": {},
     "output_type": "execute_result"
    },
    {
     "data": {
      "image/png": "iVBORw0KGgoAAAANSUhEUgAAAYUAAAEKCAYAAAD9xUlFAAAABHNCSVQICAgIfAhkiAAAAAlwSFlzAAALEgAACxIB0t1+/AAAADl0RVh0U29mdHdhcmUAbWF0cGxvdGxpYiB2ZXJzaW9uIDIuMS4yLCBodHRwOi8vbWF0cGxvdGxpYi5vcmcvNQv5yAAAEuNJREFUeJzt3X+wXHd93vH3o+uoBOM0Ad+OPJaEFRC0grgwuRF/uEMIManczEiZ4UflOE08Q9Awg4CWAcWkjQpKmSYiA0OI0kFpaAgTEI6daZSMGjXB5kdd7EgGYZCEEkUGdCVukDEGO3Ejy/70j7v6Zrm+uruy7tHea71fMzvac/a7Z5/rnfGz37N7zklVIUkSwJJRB5AkLRyWgiSpsRQkSY2lIElqLAVJUmMpSJIaS0GS1FgKkqTGUpAkNZeNOsD5uvLKK+uaa64ZdQxJWlTuvffeB6pqfNC4RVcK11xzDfv37x91DElaVJJ8bZhx7j6SJDWWgiSpsRQkSY2lIElqLAVJUtNpKSRZl+RIkqNJbpnl8fcnOdC7/VWSh7rMI0maW2c/SU0yBuwAXgVMAvuS7K6qQ2fHVNV/6Bv/ZuClXeWRJA3W5UxhLXC0qo5V1WlgF7BhjvE3Ah/vMI8kaYAuD167GjjetzwJvGy2gUmeC6wC7jjH45uATQArV66c35QLyJYtW5iammLZsmVs37591HEkXYK6nClklnV1jrEbgduq6vHZHqyqnVU1UVUT4+MDj9JetKampjhx4gRTU1OjjiLpEtVlKUwCK/qWlwMnzzF2I+46kqSR67IU9gGrk6xKspTp//HvnjkoyQuBHwI+12EWSdIQOiuFqjoDbAb2AoeBW6vqYJJtSdb3Db0R2FVV59q1JEm6SDo9S2pV7QH2zFi3dcbyu7rMIEkankc0S5IaS0GS1FgKkqTGUpAkNZaCJKmxFCRJjaUgSWosBUlSYylIkhpLQZLUWAqSpMZSkCQ1loIkqbEUJEmNpSBJajq9nsKo/eg7fn/UEc7LFQ88zBjw9QceXlTZ733vz486gqR54kxBktRYCpKkxlKQJDWWgiSp6bQUkqxLciTJ0SS3nGPM65IcSnIwyce6zCNJmltnvz5KMgbsAF4FTAL7kuyuqkN9Y1YD7wSuq6pvJ/lnXeWRJA3W5UxhLXC0qo5V1WlgF7Bhxpg3ADuq6tsAVfXNDvNIkgboshSuBo73LU/21vV7AfCCJHcluTvJug7zSJIG6PLgtcyyrmZ5/dXAK4DlwGeTvLiqHvqeDSWbgE0AK1eunP+kkiSg25nCJLCib3k5cHKWMX9cVY9V1f3AEaZL4ntU1c6qmqiqifHx8c4CS9KlrstS2AesTrIqyVJgI7B7xpj/CfwEQJIrmd6ddKzDTJKkOXRWClV1BtgM7AUOA7dW1cEk25Ks7w3bC3wrySHgTuAdVfWtrjJJkubW6QnxqmoPsGfGuq199wt4W+8mSRoxj2iWJDWWgiSpsRQkSY2lIElqLAVJUmMpSJKap/U1mhebJ5Ze/j3/StLFZiksIH+3+qdGHUHSJc7dR5KkxpmCNA+2bNnC1NQUy5YtY/v27aOOIz1lloI0D6ampjhx4sSoY0gXzN1HkqTGUpAkNZaCJKmxFCRJjaUgSWosBUlSYylIkhpLQZLUWAqSpMZSkCQ1nZZCknVJjiQ5muSWWR6/OcmpJAd6t1/sMo8kaW6dnfsoyRiwA3gVMAnsS7K7qg7NGPqJqtrcVQ5J0vC6nCmsBY5W1bGqOg3sAjZ0+HqSpAvUZSlcDRzvW57srZvp1UnuS3JbkhUd5pEkDdBlKWSWdTVj+U+Aa6rqWuAvgI/MuqFkU5L9SfafOnVqnmNKks7qshQmgf5P/suBk/0DqupbVfUPvcXfAX50tg1V1c6qmqiqifHx8U7CSpK6LYV9wOokq5IsBTYCu/sHJLmqb3E9cLjDPJKkATr79VFVnUmyGdgLjAEfrqqDSbYB+6tqN/CWJOuBM8CDwM1d5ZEkDdbp5Tirag+wZ8a6rX333wm8s8sMkqTheUSzJKmxFCRJjaUgSWo6/U5BuhBf3/Yjo44wtDMPPhu4jDMPfm1R5V659UujjqAFxpmCJKmxFCRJjaUgSWosBUlSYylIkhpLQZLUWAqSpMZSkCQ1loIkqbEUJEmNpSBJauY891GSh3nydZWbqvqBeU8kSRqZOUuhqq4A6F0tbQr4KBDgJuCKztNJki6qYXcf/euq+u2qeriqvltV/w14dZfBJEkX37Cl8HiSm5KMJVmS5Cbg8S6DSZIuvmFL4WeB1wF/27u9trdOkvQ0MtRFdqrqq8CGbqNIkkZtqJlCkhck+WSSL/eWr03yn4Z43rokR5IcTXLLHONek6SSTAwfXZI034bdffQ7wDuBxwCq6j5g41xPSDIG7ABuANYANyZZM8u4K4C3APcMH1uS1IVhS+GZVfWXM9adGfCctcDRqjpWVaeBXcy+C+pXge3A/xsyiySpI8OWwgNJnkfvQLYkrwG+MeA5VwPH+5Yne+uaJC8FVlTVn861oSSbkuxPsv/UqVNDRpYkna+hvmgG3gTsBP55khPA/UwfwDaXzLKuHR2dZAnwfuDmQS9eVTt7r8/ExMQ5j7CWJF2YYUvha1V1fZLLgSVV9fAQz5kEVvQtLwdO9i1fAbwY+FQSgGXA7iTrq2r/kLmkBeHKZzwBnOn9Ky1ew5bC/Un+DPgEcMeQz9kHrE6yCjjB9BfT7diGqvoOcOXZ5SSfAt5uIWgxevu1D406gjQvhv1O4YXAXzC9G+n+JL+V5F/N9YSqOgNsBvYCh4Fbq+pgkm1J1l9IaElSN4Y9eO1R4Fbg1iQ/BHwA+DQwNuB5e4A9M9ZtPcfYVwyTRZLUnaGvp5Dkx5P8NvB54BlMn/ZCkvQ0MtRMIcn9wAGmZwvvqKq/6zSVJGkkhv2i+V9W1Xc7TSJJGrlBV17bUlXbgfckedLxAVX1ls6SSZIuukEzhcO9f/2ZqCRdAgZdjvNPenfvq6ovXIQ8kqQRGvbXR+9L8pUkv5rkRZ0mkiSNzFClUFU/AbwCOAXsTPKlYa6nIElaXIY+TqGqpqrqN4E3Mv3z1FkPQpMkLV7DXnntXyR5V+/Ka78F/F+mT3AnSXoaGfY4hf8BfBz4qao6OWiwJGlxGlgKvctq/k1VfeAi5JEkjdDA3UdV9TjwnCRLL0IeSdIIDX2RHeCuJLuBdt6jqnpfJ6kkSSMxbCmc7N2WMH3FNEnS09Cw11N4d9dBJEmjN+yps+8EZjsh3ivnPZEkaWSG3X309r77zwBeDZyZ/ziSpFEadvfRvTNW3ZXk0x3kkSSN0LC7j57dt7gEmACWdZJIkjQyw+4+upd//E7hDPBV4PWDnpRkHfABYAz471X1azMefyPwJuBx4BFgU1UdGjKTJGmezXnwWpIfS7KsqlZV1Q8D7wa+0rvN+T/v3pHQO4AbgDXAjUnWzBj2sar6kap6CbAd8LgHSRqhQUc0fwg4DZDk5cB/BT4CfAfYOeC5a4GjVXWsqk4Du4AN/QNmXPf5cmb5hZMk6eIZtPtorKoe7N3/t8DOqroduD3JgQHPvRo43rc8Cbxs5qAkbwLeBiwF/ImrJI3QoJnCWJKzxfGTwB19jw0qlMyybrZjHXZU1fOAXwJmvXBPkk1J9ifZf+rUqQEvK0l6qgaVwseBTyf5Y+BR4LMASZ7P9C6kuUwCK/qWlzN9qoxz2QX8zGwPVNXOqpqoqonx8fEBLytJeqrm/LRfVe9J8kngKuB/V9XZT/pLgDcP2PY+YHWSVcAJYCPws/0Dkqyuqr/uLf408NdIkkZm4E9Sq+ruWdb91RDPO5NkM7CX6Z+kfriqDibZBuyvqt3A5iTXA48B3wZ+4Xz/AEnS/Bn2OIWnpKr2AHtmrNvad/+tXb6+JOn8dFoKkrQYbNmyhampKZYtW8b27dtHHWekLAVJl7ypqSlOnDgx6hgLwsDLcUqSLh2WgiSpsRQkSY2lIElqLAVJUmMpSJIaS0GS1FgKkqTGUpAkNZaCJKnxNBeS5t11H7xu1BHOy9KHlrKEJRx/6Piiyn7Xm++a9206U5AkNZaCJKmxFCRJjaUgSWosBUlSYylIkhpLQZLUWAqSpKbTUkiyLsmRJEeT3DLL429LcijJfUk+meS5XeaRJM2ts1JIMgbsAG4A1gA3JlkzY9gXgImquha4DdjeVR5J0mBdzhTWAker6lhVnQZ2ARv6B1TVnVX1973Fu4HlHeaRJA3QZSlcDRzvW57srTuX1wP/q8M8kjSrembxxOVPUM+sUUcZuS5PiJdZ1s36XzzJzwETwI+f4/FNwCaAlStXzlc+SQLgseseG3WEBaPLmcIksKJveTlwcuagJNcD/xFYX1X/MNuGqmpnVU1U1cT4+HgnYSVJ3ZbCPmB1klVJlgIbgd39A5K8FPgQ04XwzQ6zSJKG0FkpVNUZYDOwFzgM3FpVB5NsS7K+N+y9wLOAP0xyIMnuc2xOknQRdHqRnaraA+yZsW5r3/3ru3x9SdL58YhmSVJjKUiSGktBktRYCpKkxlKQJDWWgiSpsRQkSY2lIElqLAVJUmMpSJIaS0GS1FgKkqTGUpAkNZaCJKmxFCRJjaUgSWosBUlSYylIkhpLQZLUWAqSpMZSkCQ1nZZCknVJjiQ5muSWWR5/eZLPJzmT5DVdZpEkDdZZKSQZA3YANwBrgBuTrJkx7OvAzcDHusohSRreZR1uey1wtKqOASTZBWwADp0dUFVf7T32RIc5JElD6nL30dXA8b7lyd6685ZkU5L9SfafOnVqXsJJkp6sy1LILOvqqWyoqnZW1URVTYyPj19gLEnSuXRZCpPAir7l5cDJDl9PknSBuiyFfcDqJKuSLAU2Ars7fD1J0gXqrBSq6gywGdgLHAZuraqDSbYlWQ+Q5MeSTAKvBT6U5GBXeSRJg3X56yOqag+wZ8a6rX339zG9W0mStAB4RLMkqbEUJEmNpSBJaiwFSVJjKUiSGktBktRYCpKkxlKQJDWWgiSpsRQkSY2lIElqLAVJUmMpSJIaS0GS1FgKkqTGUpAkNZaCJKmxFCRJjaUgSWosBUlS02kpJFmX5EiSo0lumeXxf5LkE73H70lyTZd5JElz66wUkowBO4AbgDXAjUnWzBj2euDbVfV84P3Ar3eVR5I0WJczhbXA0ao6VlWngV3AhhljNgAf6d2/DfjJJOkwkyRpDl2WwtXA8b7lyd66WcdU1RngO8BzOswkSZrDZR1ue7ZP/PUUxpBkE7Cpt/hIkiMXmG0huxJ4YNQhzkd+4xdGHWGhWHTvHf/ZiXmfRff+5S3n9f49d5hBXZbCJLCib3k5cPIcYyaTXAb8U+DBmRuqqp3Azo5yLihJ9lfVxKhz6Pz53i1uvn/Tutx9tA9YnWRVkqXARmD3jDG7gbMfM18D3FFVT5opSJIujs5mClV1JslmYC8wBny4qg4m2Qbsr6rdwO8CH01ylOkZwsau8kiSBosfzBeWJJt6u8u0yPjeLW6+f9MsBUlS42kuJEmNpbBAJPlwkm8m+fKos+j8JFmR5M4kh5McTPLWUWfS8JI8I8lfJvli7/1796gzjZK7jxaIJC8HHgF+v6pePOo8Gl6Sq4CrqurzSa4A7gV+pqoOjTiahtA7i8LlVfVIku8D/g/w1qq6e8TRRsKZwgJRVZ9hlmM0tPBV1Teq6vO9+w8Dh3ny0ftaoGraI73F7+vdLtlPy5aCNI96Z/p9KXDPaJPofCQZS3IA+Cbw51V1yb5/loI0T5I8C7gd+PdV9d1R59HwqurxqnoJ02deWJvkkt2FaylI86C3L/p24A+q6o9GnUdPTVU9BHwKWDfiKCNjKUgXqPdF5e8Ch6vqfaPOo/OTZDzJD/bufz9wPfCV0aYaHUthgUjyceBzwAuTTCZ5/agzaWjXAf8OeGWSA73bvxl1KA3tKuDOJPcxfc62P6+qPx1xppHxJ6mSpMaZgiSpsRQkSY2lIElqLAVJUmMpSJIaS0GaIcnjvZ+VfjnJHyZ55hxj35Xk7Rczn9QlS0F6sker6iW9s9WeBt446kDSxWIpSHP7LPB8gCQ/n+S+3nn3PzpzYJI3JNnXe/z2szOMJK/tzTq+mOQzvXUv6p3D/0Bvm6sv6l8lnYMHr0kzJHmkqp6V5DKmz2f0Z8BngD8CrquqB5I8u6oeTPIu4JGq+o0kz6mqb/W28V+Av62qDyb5ErCuqk4k+cGqeijJB4G7q+oPkiwFxqrq0ZH8wVIfZwrSk31/7zTK+4GvM31eo1cCt1XVAwBVNdu1L16c5LO9ErgJeFFv/V3A7yV5AzDWW/c54JeT/BLwXAtBC8Vlow4gLUCP9k6j3PROejdoWv17TF9x7YtJbgZeAVBVb0zyMuCngQNJXlJVH0tyT2/d3iS/WFV3zPPfIZ03ZwrScD4JvC7JcwCSPHuWMVcA3+idRvumsyuTPK+q7qmqrcADwIokPwwcq6rfBHYD13b+F0hDcKYgDaGqDiZ5D/DpJI8DXwBunjHsV5i+4trXgC8xXRIA7+19kRymy+WLwC3AzyV5DJgCtnX+R0hD8ItmSVLj7iNJUmMpSJIaS0GS1FgKkqTGUpAkNZaCJKmxFCRJjaUgSWr+P9/oIrQSFReeAAAAAElFTkSuQmCC\n",
      "text/plain": [
       "<matplotlib.figure.Figure at 0x20c835c3518>"
      ]
     },
     "metadata": {},
     "output_type": "display_data"
    }
   ],
   "source": [
    "sns.barplot(x='Pclass', y='Survived', data=train_df)"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "Here we see clearly, that Pclass is contributing to a persons chance of survival, especially if this person is in class 1. We will create another pclass plot below."
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 231,
   "metadata": {},
   "outputs": [
    {
     "data": {
      "image/png": "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\n",
      "text/plain": [
       "<matplotlib.figure.Figure at 0x20c83981940>"
      ]
     },
     "metadata": {},
     "output_type": "display_data"
    }
   ],
   "source": [
    "grid = sns.FacetGrid(train_df, col='Survived', row='Pclass', size=2.2, aspect=1.6)\n",
    "grid.map(plt.hist, 'Age', alpha=.5, bins=20)\n",
    "grid.add_legend();"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "The plot above confirms our assumption about pclass 1, but we can also spot a high probability that a person in pclass 3 will not survive."
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "**5.  SibSp and Parch:**"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "SibSp and Parch would make more sense as a combined feature, that shows the total number of relatives, a person has on the Titanic. I will create it below and also a feature that sows if someone is not alone."
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 232,
   "metadata": {},
   "outputs": [],
   "source": [
    "data = [train_df, test_df]\n",
    "for dataset in data:\n",
    "    dataset['relatives'] = dataset['SibSp'] + dataset['Parch']\n",
    "    dataset.loc[dataset['relatives'] > 0, 'not_alone'] = 0\n",
    "    dataset.loc[dataset['relatives'] == 0, 'not_alone'] = 1\n",
    "    dataset['not_alone'] = dataset['not_alone'].astype(int)"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 233,
   "metadata": {},
   "outputs": [
    {
     "data": {
      "text/html": [
       "<div>\n",
       "<style scoped>\n",
       "    .dataframe tbody tr th:only-of-type {\n",
       "        vertical-align: middle;\n",
       "    }\n",
       "\n",
       "    .dataframe tbody tr th {\n",
       "        vertical-align: top;\n",
       "    }\n",
       "\n",
       "    .dataframe thead th {\n",
       "        text-align: right;\n",
       "    }\n",
       "</style>\n",
       "<table border=\"1\" class=\"dataframe\">\n",
       "  <thead>\n",
       "    <tr style=\"text-align: right;\">\n",
       "      <th></th>\n",
       "      <th>PassengerId</th>\n",
       "      <th>Survived</th>\n",
       "      <th>Pclass</th>\n",
       "      <th>Name</th>\n",
       "      <th>Sex</th>\n",
       "      <th>Age</th>\n",
       "      <th>SibSp</th>\n",
       "      <th>Parch</th>\n",
       "      <th>Ticket</th>\n",
       "      <th>Fare</th>\n",
       "      <th>Cabin</th>\n",
       "      <th>Embarked</th>\n",
       "      <th>relatives</th>\n",
       "      <th>not_alone</th>\n",
       "    </tr>\n",
       "  </thead>\n",
       "  <tbody>\n",
       "    <tr>\n",
       "      <th>0</th>\n",
       "      <td>1</td>\n",
       "      <td>0</td>\n",
       "      <td>3</td>\n",
       "      <td>Braund, Mr. Owen Harris</td>\n",
       "      <td>male</td>\n",
       "      <td>22.0</td>\n",
       "      <td>1</td>\n",
       "      <td>0</td>\n",
       "      <td>A/5 21171</td>\n",
       "      <td>7.2500</td>\n",
       "      <td>NaN</td>\n",
       "      <td>S</td>\n",
       "      <td>1</td>\n",
       "      <td>0</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>1</th>\n",
       "      <td>2</td>\n",
       "      <td>1</td>\n",
       "      <td>1</td>\n",
       "      <td>Cumings, Mrs. John Bradley (Florence Briggs Th...</td>\n",
       "      <td>female</td>\n",
       "      <td>38.0</td>\n",
       "      <td>1</td>\n",
       "      <td>0</td>\n",
       "      <td>PC 17599</td>\n",
       "      <td>71.2833</td>\n",
       "      <td>C85</td>\n",
       "      <td>C</td>\n",
       "      <td>1</td>\n",
       "      <td>0</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>2</th>\n",
       "      <td>3</td>\n",
       "      <td>1</td>\n",
       "      <td>3</td>\n",
       "      <td>Heikkinen, Miss. Laina</td>\n",
       "      <td>female</td>\n",
       "      <td>26.0</td>\n",
       "      <td>0</td>\n",
       "      <td>0</td>\n",
       "      <td>STON/O2. 3101282</td>\n",
       "      <td>7.9250</td>\n",
       "      <td>NaN</td>\n",
       "      <td>S</td>\n",
       "      <td>0</td>\n",
       "      <td>1</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>3</th>\n",
       "      <td>4</td>\n",
       "      <td>1</td>\n",
       "      <td>1</td>\n",
       "      <td>Futrelle, Mrs. Jacques Heath (Lily May Peel)</td>\n",
       "      <td>female</td>\n",
       "      <td>35.0</td>\n",
       "      <td>1</td>\n",
       "      <td>0</td>\n",
       "      <td>113803</td>\n",
       "      <td>53.1000</td>\n",
       "      <td>C123</td>\n",
       "      <td>S</td>\n",
       "      <td>1</td>\n",
       "      <td>0</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>4</th>\n",
       "      <td>5</td>\n",
       "      <td>0</td>\n",
       "      <td>3</td>\n",
       "      <td>Allen, Mr. William Henry</td>\n",
       "      <td>male</td>\n",
       "      <td>35.0</td>\n",
       "      <td>0</td>\n",
       "      <td>0</td>\n",
       "      <td>373450</td>\n",
       "      <td>8.0500</td>\n",
       "      <td>NaN</td>\n",
       "      <td>S</td>\n",
       "      <td>0</td>\n",
       "      <td>1</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>5</th>\n",
       "      <td>6</td>\n",
       "      <td>0</td>\n",
       "      <td>3</td>\n",
       "      <td>Moran, Mr. James</td>\n",
       "      <td>male</td>\n",
       "      <td>NaN</td>\n",
       "      <td>0</td>\n",
       "      <td>0</td>\n",
       "      <td>330877</td>\n",
       "      <td>8.4583</td>\n",
       "      <td>NaN</td>\n",
       "      <td>Q</td>\n",
       "      <td>0</td>\n",
       "      <td>1</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>6</th>\n",
       "      <td>7</td>\n",
       "      <td>0</td>\n",
       "      <td>1</td>\n",
       "      <td>McCarthy, Mr. Timothy J</td>\n",
       "      <td>male</td>\n",
       "      <td>54.0</td>\n",
       "      <td>0</td>\n",
       "      <td>0</td>\n",
       "      <td>17463</td>\n",
       "      <td>51.8625</td>\n",
       "      <td>E46</td>\n",
       "      <td>S</td>\n",
       "      <td>0</td>\n",
       "      <td>1</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>7</th>\n",
       "      <td>8</td>\n",
       "      <td>0</td>\n",
       "      <td>3</td>\n",
       "      <td>Palsson, Master. Gosta Leonard</td>\n",
       "      <td>male</td>\n",
       "      <td>2.0</td>\n",
       "      <td>3</td>\n",
       "      <td>1</td>\n",
       "      <td>349909</td>\n",
       "      <td>21.0750</td>\n",
       "      <td>NaN</td>\n",
       "      <td>S</td>\n",
       "      <td>4</td>\n",
       "      <td>0</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>8</th>\n",
       "      <td>9</td>\n",
       "      <td>1</td>\n",
       "      <td>3</td>\n",
       "      <td>Johnson, Mrs. Oscar W (Elisabeth Vilhelmina Berg)</td>\n",
       "      <td>female</td>\n",
       "      <td>27.0</td>\n",
       "      <td>0</td>\n",
       "      <td>2</td>\n",
       "      <td>347742</td>\n",
       "      <td>11.1333</td>\n",
       "      <td>NaN</td>\n",
       "      <td>S</td>\n",
       "      <td>2</td>\n",
       "      <td>0</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>9</th>\n",
       "      <td>10</td>\n",
       "      <td>1</td>\n",
       "      <td>2</td>\n",
       "      <td>Nasser, Mrs. Nicholas (Adele Achem)</td>\n",
       "      <td>female</td>\n",
       "      <td>14.0</td>\n",
       "      <td>1</td>\n",
       "      <td>0</td>\n",
       "      <td>237736</td>\n",
       "      <td>30.0708</td>\n",
       "      <td>NaN</td>\n",
       "      <td>C</td>\n",
       "      <td>1</td>\n",
       "      <td>0</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>10</th>\n",
       "      <td>11</td>\n",
       "      <td>1</td>\n",
       "      <td>3</td>\n",
       "      <td>Sandstrom, Miss. Marguerite Rut</td>\n",
       "      <td>female</td>\n",
       "      <td>4.0</td>\n",
       "      <td>1</td>\n",
       "      <td>1</td>\n",
       "      <td>PP 9549</td>\n",
       "      <td>16.7000</td>\n",
       "      <td>G6</td>\n",
       "      <td>S</td>\n",
       "      <td>2</td>\n",
       "      <td>0</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>11</th>\n",
       "      <td>12</td>\n",
       "      <td>1</td>\n",
       "      <td>1</td>\n",
       "      <td>Bonnell, Miss. Elizabeth</td>\n",
       "      <td>female</td>\n",
       "      <td>58.0</td>\n",
       "      <td>0</td>\n",
       "      <td>0</td>\n",
       "      <td>113783</td>\n",
       "      <td>26.5500</td>\n",
       "      <td>C103</td>\n",
       "      <td>S</td>\n",
       "      <td>0</td>\n",
       "      <td>1</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>12</th>\n",
       "      <td>13</td>\n",
       "      <td>0</td>\n",
       "      <td>3</td>\n",
       "      <td>Saundercock, Mr. William Henry</td>\n",
       "      <td>male</td>\n",
       "      <td>20.0</td>\n",
       "      <td>0</td>\n",
       "      <td>0</td>\n",
       "      <td>A/5. 2151</td>\n",
       "      <td>8.0500</td>\n",
       "      <td>NaN</td>\n",
       "      <td>S</td>\n",
       "      <td>0</td>\n",
       "      <td>1</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>13</th>\n",
       "      <td>14</td>\n",
       "      <td>0</td>\n",
       "      <td>3</td>\n",
       "      <td>Andersson, Mr. Anders Johan</td>\n",
       "      <td>male</td>\n",
       "      <td>39.0</td>\n",
       "      <td>1</td>\n",
       "      <td>5</td>\n",
       "      <td>347082</td>\n",
       "      <td>31.2750</td>\n",
       "      <td>NaN</td>\n",
       "      <td>S</td>\n",
       "      <td>6</td>\n",
       "      <td>0</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>14</th>\n",
       "      <td>15</td>\n",
       "      <td>0</td>\n",
       "      <td>3</td>\n",
       "      <td>Vestrom, Miss. Hulda Amanda Adolfina</td>\n",
       "      <td>female</td>\n",
       "      <td>14.0</td>\n",
       "      <td>0</td>\n",
       "      <td>0</td>\n",
       "      <td>350406</td>\n",
       "      <td>7.8542</td>\n",
       "      <td>NaN</td>\n",
       "      <td>S</td>\n",
       "      <td>0</td>\n",
       "      <td>1</td>\n",
       "    </tr>\n",
       "  </tbody>\n",
       "</table>\n",
       "</div>"
      ],
      "text/plain": [
       "    PassengerId  Survived  Pclass  \\\n",
       "0             1         0       3   \n",
       "1             2         1       1   \n",
       "2             3         1       3   \n",
       "3             4         1       1   \n",
       "4             5         0       3   \n",
       "5             6         0       3   \n",
       "6             7         0       1   \n",
       "7             8         0       3   \n",
       "8             9         1       3   \n",
       "9            10         1       2   \n",
       "10           11         1       3   \n",
       "11           12         1       1   \n",
       "12           13         0       3   \n",
       "13           14         0       3   \n",
       "14           15         0       3   \n",
       "\n",
       "                                                 Name     Sex   Age  SibSp  \\\n",
       "0                             Braund, Mr. Owen Harris    male  22.0      1   \n",
       "1   Cumings, Mrs. John Bradley (Florence Briggs Th...  female  38.0      1   \n",
       "2                              Heikkinen, Miss. Laina  female  26.0      0   \n",
       "3        Futrelle, Mrs. Jacques Heath (Lily May Peel)  female  35.0      1   \n",
       "4                            Allen, Mr. William Henry    male  35.0      0   \n",
       "5                                    Moran, Mr. James    male   NaN      0   \n",
       "6                             McCarthy, Mr. Timothy J    male  54.0      0   \n",
       "7                      Palsson, Master. Gosta Leonard    male   2.0      3   \n",
       "8   Johnson, Mrs. Oscar W (Elisabeth Vilhelmina Berg)  female  27.0      0   \n",
       "9                 Nasser, Mrs. Nicholas (Adele Achem)  female  14.0      1   \n",
       "10                    Sandstrom, Miss. Marguerite Rut  female   4.0      1   \n",
       "11                           Bonnell, Miss. Elizabeth  female  58.0      0   \n",
       "12                     Saundercock, Mr. William Henry    male  20.0      0   \n",
       "13                        Andersson, Mr. Anders Johan    male  39.0      1   \n",
       "14               Vestrom, Miss. Hulda Amanda Adolfina  female  14.0      0   \n",
       "\n",
       "    Parch            Ticket     Fare Cabin Embarked  relatives  not_alone  \n",
       "0       0         A/5 21171   7.2500   NaN        S          1          0  \n",
       "1       0          PC 17599  71.2833   C85        C          1          0  \n",
       "2       0  STON/O2. 3101282   7.9250   NaN        S          0          1  \n",
       "3       0            113803  53.1000  C123        S          1          0  \n",
       "4       0            373450   8.0500   NaN        S          0          1  \n",
       "5       0            330877   8.4583   NaN        Q          0          1  \n",
       "6       0             17463  51.8625   E46        S          0          1  \n",
       "7       1            349909  21.0750   NaN        S          4          0  \n",
       "8       2            347742  11.1333   NaN        S          2          0  \n",
       "9       0            237736  30.0708   NaN        C          1          0  \n",
       "10      1           PP 9549  16.7000    G6        S          2          0  \n",
       "11      0            113783  26.5500  C103        S          0          1  \n",
       "12      0         A/5. 2151   8.0500   NaN        S          0          1  \n",
       "13      5            347082  31.2750   NaN        S          6          0  \n",
       "14      0            350406   7.8542   NaN        S          0          1  "
      ]
     },
     "execution_count": 233,
     "metadata": {},
     "output_type": "execute_result"
    }
   ],
   "source": [
    "train_df.head(15)"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 234,
   "metadata": {},
   "outputs": [
    {
     "data": {
      "text/plain": [
       "1    537\n",
       "0    354\n",
       "Name: not_alone, dtype: int64"
      ]
     },
     "execution_count": 234,
     "metadata": {},
     "output_type": "execute_result"
    }
   ],
   "source": [
    "train_df['not_alone'].value_counts()"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 235,
   "metadata": {},
   "outputs": [
    {
     "data": {
      "image/png": "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\n",
      "text/plain": [
       "<matplotlib.figure.Figure at 0x20c863ca1d0>"
      ]
     },
     "metadata": {},
     "output_type": "display_data"
    }
   ],
   "source": [
    "axes = sns.factorplot('relatives','Survived', \n",
    "                      data=train_df, aspect = 2.5)"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "Here we can see that you had a high probabilty of survival with 1 to 3 realitves, but a lower one if you had less than 1 or more than 3 (except for some cases with 6 relatives)."
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "### **Data Preprocessing**"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "First, I will drop 'PassengerId' from the train set, because it does not contribute to a persons survival probability. I will not drop it from the test set, since it is required there for the submission"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 236,
   "metadata": {},
   "outputs": [],
   "source": [
    "train_df.head()\n",
    "train_df = train_df.drop(['PassengerId'], axis=1)"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "## Missing Data:\n",
    "### Cabin:\n",
    "As a reminder, we have to deal with Cabin (687), Embarked (2) and Age (177). \n",
    "\n",
    "First I thought, we have to delete the 'Cabin' variable but then I found something interesting. A cabin number looks like ‘C123’ and the **letter refers to the deck**. \n",
    "\n",
    "Therefore we’re going to extract these and create a new feature, that contains a persons deck. Afterwords we will convert the feature into a numeric variable. The missing values will be converted to zero.\n",
    "\n"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 237,
   "metadata": {},
   "outputs": [],
   "source": [
    "import re\n",
    "deck = {\"A\": 1, \"B\": 2, \"C\": 3, \"D\": 4, \"E\": 5, \"F\": 6, \"G\": 7, \"U\": 8}\n",
    "data = [train_df, test_df]\n",
    "\n",
    "for dataset in data:\n",
    "    dataset['Cabin'] = dataset['Cabin'].fillna(\"U0\")\n",
    "    dataset['Deck'] = dataset['Cabin'].map(lambda x: re.compile(\"([a-zA-Z]+)\").search(x).group())\n",
    "    dataset['Deck'] = dataset['Deck'].map(deck)\n",
    "    dataset['Deck'] = dataset['Deck'].fillna(0)\n",
    "    dataset['Deck'] = dataset['Deck'].astype(int) "
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 238,
   "metadata": {},
   "outputs": [],
   "source": [
    "# we can now drop the cabin feature\n",
    "train_df = train_df.drop(['Cabin'], axis=1)\n",
    "test_df = test_df.drop(['Cabin'], axis=1)"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "### Age:\n",
    "\n",
    "Now we can tackle the issue with the age features missing values. I will create an array that contains random numbers, which are computed based on the mean age value in regards to the standard deviation and is_null."
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 239,
   "metadata": {},
   "outputs": [],
   "source": [
    "data = [train_df, test_df]\n",
    "\n",
    "for dataset in data:\n",
    "    mean = train_df[\"Age\"].mean()\n",
    "    std = test_df[\"Age\"].std()\n",
    "    is_null = dataset[\"Age\"].isnull().sum()\n",
    "    # compute random numbers between the mean, std and is_null\n",
    "    rand_age = np.random.randint(mean - std, mean + std, size = is_null)\n",
    "    # fill NaN values in Age column with random values generated\n",
    "    age_slice = dataset[\"Age\"].copy()\n",
    "    age_slice[np.isnan(age_slice)] = rand_age\n",
    "    dataset[\"Age\"] = age_slice\n",
    "    dataset[\"Age\"] = train_df[\"Age\"].astype(int)"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 240,
   "metadata": {},
   "outputs": [
    {
     "data": {
      "text/plain": [
       "0"
      ]
     },
     "execution_count": 240,
     "metadata": {},
     "output_type": "execute_result"
    }
   ],
   "source": [
    "train_df[\"Age\"].isnull().sum()"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "### Embarked:\n",
    "\n",
    "Since the Embarked feature has only 2 missing values, we will just fill these with the most common one."
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 241,
   "metadata": {},
   "outputs": [
    {
     "data": {
      "text/plain": [
       "count     889\n",
       "unique      3\n",
       "top         S\n",
       "freq      644\n",
       "Name: Embarked, dtype: object"
      ]
     },
     "execution_count": 241,
     "metadata": {},
     "output_type": "execute_result"
    }
   ],
   "source": [
    "train_df['Embarked'].describe()"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 242,
   "metadata": {},
   "outputs": [],
   "source": [
    "common_value = 'S'\n",
    "data = [train_df, test_df]\n",
    "\n",
    "for dataset in data:\n",
    "    dataset['Embarked'] = dataset['Embarked'].fillna(common_value)"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "## Converting Features:"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 243,
   "metadata": {},
   "outputs": [
    {
     "name": "stdout",
     "output_type": "stream",
     "text": [
      "<class 'pandas.core.frame.DataFrame'>\n",
      "RangeIndex: 891 entries, 0 to 890\n",
      "Data columns (total 13 columns):\n",
      "Survived     891 non-null int64\n",
      "Pclass       891 non-null int64\n",
      "Name         891 non-null object\n",
      "Sex          891 non-null object\n",
      "Age          891 non-null int32\n",
      "SibSp        891 non-null int64\n",
      "Parch        891 non-null int64\n",
      "Ticket       891 non-null object\n",
      "Fare         891 non-null float64\n",
      "Embarked     891 non-null object\n",
      "relatives    891 non-null int64\n",
      "not_alone    891 non-null int32\n",
      "Deck         891 non-null int32\n",
      "dtypes: float64(1), int32(3), int64(5), object(4)\n",
      "memory usage: 80.1+ KB\n"
     ]
    }
   ],
   "source": [
    "train_df.info()"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "Above you can see that 'Fare' is a float and we have to deal with 4 categorical features: Name, Sex, Ticket and Embarked. Lets investigate and transfrom one after another."
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "### Fare:\n",
    "\n",
    "Converting \"Fare\" from float to int64, using the \"astype()\" function pandas provides:"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 244,
   "metadata": {},
   "outputs": [],
   "source": [
    "data = [train_df, test_df]\n",
    "\n",
    "for dataset in data:\n",
    "    dataset['Fare'] = dataset['Fare'].fillna(0)\n",
    "    dataset['Fare'] = dataset['Fare'].astype(int)"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "metadata": {},
   "outputs": [],
   "source": []
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "### Name:\n",
    "We will use the Name feature to extract the Titles from the Name, so that we can build a new feature out of that."
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 245,
   "metadata": {},
   "outputs": [],
   "source": [
    "data = [train_df, test_df]\n",
    "titles = {\"Mr\": 1, \"Miss\": 2, \"Mrs\": 3, \"Master\": 4, \"Rare\": 5}\n",
    "\n",
    "for dataset in data:\n",
    "    # extract titles\n",
    "    dataset['Title'] = dataset.Name.str.extract(' ([A-Za-z]+)\\.', expand=False)\n",
    "    # replace titles with a more common title or as Rare\n",
    "    dataset['Title'] = dataset['Title'].replace(['Lady', 'Countess','Capt', 'Col','Don', 'Dr',\\\n",
    "                                            'Major', 'Rev', 'Sir', 'Jonkheer', 'Dona'], 'Rare')\n",
    "    dataset['Title'] = dataset['Title'].replace('Mlle', 'Miss')\n",
    "    dataset['Title'] = dataset['Title'].replace('Ms', 'Miss')\n",
    "    dataset['Title'] = dataset['Title'].replace('Mme', 'Mrs')\n",
    "    # convert titles into numbers\n",
    "    dataset['Title'] = dataset['Title'].map(titles)\n",
    "    # filling NaN with 0, to get safe\n",
    "    dataset['Title'] = dataset['Title'].fillna(0)"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 246,
   "metadata": {},
   "outputs": [],
   "source": [
    "train_df = train_df.drop(['Name'], axis=1)\n",
    "test_df = test_df.drop(['Name'], axis=1)"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "### Sex:\n",
    "\n",
    "Convert 'Sex' feature into numeric."
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 247,
   "metadata": {},
   "outputs": [],
   "source": [
    "genders = {\"male\": 0, \"female\": 1}\n",
    "data = [train_df, test_df]\n",
    "\n",
    "for dataset in data:\n",
    "    dataset['Sex'] = dataset['Sex'].map(genders)"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "### Ticket:"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 248,
   "metadata": {},
   "outputs": [
    {
     "data": {
      "text/plain": [
       "count        891\n",
       "unique       681\n",
       "top       347082\n",
       "freq           7\n",
       "Name: Ticket, dtype: object"
      ]
     },
     "execution_count": 248,
     "metadata": {},
     "output_type": "execute_result"
    }
   ],
   "source": [
    "train_df['Ticket'].describe()"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "Since the Ticket attribute has 681 unique tickets, it will be a bit tricky to convert them into useful categories. So we will drop it from the dataset."
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 249,
   "metadata": {},
   "outputs": [],
   "source": [
    "train_df = train_df.drop(['Ticket'], axis=1)\n",
    "test_df = test_df.drop(['Ticket'], axis=1)"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "### Embarked:\n",
    "Convert 'Embarked' feature into numeric."
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 250,
   "metadata": {},
   "outputs": [],
   "source": [
    "ports = {\"S\": 0, \"C\": 1, \"Q\": 2}\n",
    "data = [train_df, test_df]\n",
    "\n",
    "for dataset in data:\n",
    "    dataset['Embarked'] = dataset['Embarked'].map(ports)"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "## Creating Categories:\n",
    "\n",
    "We will now create categories within the following features:\n",
    "\n",
    "### Age:\n",
    "Now we need to convert the 'age' feature. First we will convert it from float into integer. Then we will create the new 'AgeGroup\" variable, by categorizing every age into a group. Note that it is important to place attention on how you form these groups, since you don't want for example that 80% of your data falls into group 1."
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 251,
   "metadata": {},
   "outputs": [],
   "source": [
    "data = [train_df, test_df]\n",
    "for dataset in data:\n",
    "    dataset['Age'] = dataset['Age'].astype(int)\n",
    "    dataset.loc[ dataset['Age'] <= 11, 'Age'] = 0\n",
    "    dataset.loc[(dataset['Age'] > 11) & (dataset['Age'] <= 18), 'Age'] = 1\n",
    "    dataset.loc[(dataset['Age'] > 18) & (dataset['Age'] <= 22), 'Age'] = 2\n",
    "    dataset.loc[(dataset['Age'] > 22) & (dataset['Age'] <= 27), 'Age'] = 3\n",
    "    dataset.loc[(dataset['Age'] > 27) & (dataset['Age'] <= 33), 'Age'] = 4\n",
    "    dataset.loc[(dataset['Age'] > 33) & (dataset['Age'] <= 40), 'Age'] = 5\n",
    "    dataset.loc[(dataset['Age'] > 40) & (dataset['Age'] <= 66), 'Age'] = 6\n",
    "    dataset.loc[ dataset['Age'] > 66, 'Age'] = 6"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 252,
   "metadata": {},
   "outputs": [
    {
     "data": {
      "text/plain": [
       "4    170\n",
       "6    158\n",
       "5    145\n",
       "3    132\n",
       "2    119\n",
       "1     99\n",
       "0     68\n",
       "Name: Age, dtype: int64"
      ]
     },
     "execution_count": 252,
     "metadata": {},
     "output_type": "execute_result"
    }
   ],
   "source": [
    "# let's see how it's distributed\n",
    "train_df['Age'].value_counts()"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "### Fare:\n",
    "For the 'Fare' feature, we need to do the same as with the 'Age' feature. \n",
    "But it isn't that easy, because if we cut the range of the fare values into a few equally big categories, 80% of the values would fall into the first category. Fortunately, we can use sklearn \"qcut()\" function, that we can use to see, how we can form the categories."
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 253,
   "metadata": {},
   "outputs": [
    {
     "data": {
      "text/html": [
       "<div>\n",
       "<style scoped>\n",
       "    .dataframe tbody tr th:only-of-type {\n",
       "        vertical-align: middle;\n",
       "    }\n",
       "\n",
       "    .dataframe tbody tr th {\n",
       "        vertical-align: top;\n",
       "    }\n",
       "\n",
       "    .dataframe thead th {\n",
       "        text-align: right;\n",
       "    }\n",
       "</style>\n",
       "<table border=\"1\" class=\"dataframe\">\n",
       "  <thead>\n",
       "    <tr style=\"text-align: right;\">\n",
       "      <th></th>\n",
       "      <th>Survived</th>\n",
       "      <th>Pclass</th>\n",
       "      <th>Sex</th>\n",
       "      <th>Age</th>\n",
       "      <th>SibSp</th>\n",
       "      <th>Parch</th>\n",
       "      <th>Fare</th>\n",
       "      <th>Embarked</th>\n",
       "      <th>relatives</th>\n",
       "      <th>not_alone</th>\n",
       "      <th>Deck</th>\n",
       "      <th>Title</th>\n",
       "    </tr>\n",
       "  </thead>\n",
       "  <tbody>\n",
       "    <tr>\n",
       "      <th>0</th>\n",
       "      <td>0</td>\n",
       "      <td>3</td>\n",
       "      <td>0</td>\n",
       "      <td>2</td>\n",
       "      <td>1</td>\n",
       "      <td>0</td>\n",
       "      <td>7</td>\n",
       "      <td>0</td>\n",
       "      <td>1</td>\n",
       "      <td>0</td>\n",
       "      <td>8</td>\n",
       "      <td>1</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>1</th>\n",
       "      <td>1</td>\n",
       "      <td>1</td>\n",
       "      <td>1</td>\n",
       "      <td>5</td>\n",
       "      <td>1</td>\n",
       "      <td>0</td>\n",
       "      <td>71</td>\n",
       "      <td>1</td>\n",
       "      <td>1</td>\n",
       "      <td>0</td>\n",
       "      <td>3</td>\n",
       "      <td>3</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>2</th>\n",
       "      <td>1</td>\n",
       "      <td>3</td>\n",
       "      <td>1</td>\n",
       "      <td>3</td>\n",
       "      <td>0</td>\n",
       "      <td>0</td>\n",
       "      <td>7</td>\n",
       "      <td>0</td>\n",
       "      <td>0</td>\n",
       "      <td>1</td>\n",
       "      <td>8</td>\n",
       "      <td>2</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>3</th>\n",
       "      <td>1</td>\n",
       "      <td>1</td>\n",
       "      <td>1</td>\n",
       "      <td>5</td>\n",
       "      <td>1</td>\n",
       "      <td>0</td>\n",
       "      <td>53</td>\n",
       "      <td>0</td>\n",
       "      <td>1</td>\n",
       "      <td>0</td>\n",
       "      <td>3</td>\n",
       "      <td>3</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>4</th>\n",
       "      <td>0</td>\n",
       "      <td>3</td>\n",
       "      <td>0</td>\n",
       "      <td>5</td>\n",
       "      <td>0</td>\n",
       "      <td>0</td>\n",
       "      <td>8</td>\n",
       "      <td>0</td>\n",
       "      <td>0</td>\n",
       "      <td>1</td>\n",
       "      <td>8</td>\n",
       "      <td>1</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>5</th>\n",
       "      <td>0</td>\n",
       "      <td>3</td>\n",
       "      <td>0</td>\n",
       "      <td>4</td>\n",
       "      <td>0</td>\n",
       "      <td>0</td>\n",
       "      <td>8</td>\n",
       "      <td>2</td>\n",
       "      <td>0</td>\n",
       "      <td>1</td>\n",
       "      <td>8</td>\n",
       "      <td>1</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>6</th>\n",
       "      <td>0</td>\n",
       "      <td>1</td>\n",
       "      <td>0</td>\n",
       "      <td>6</td>\n",
       "      <td>0</td>\n",
       "      <td>0</td>\n",
       "      <td>51</td>\n",
       "      <td>0</td>\n",
       "      <td>0</td>\n",
       "      <td>1</td>\n",
       "      <td>5</td>\n",
       "      <td>1</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>7</th>\n",
       "      <td>0</td>\n",
       "      <td>3</td>\n",
       "      <td>0</td>\n",
       "      <td>0</td>\n",
       "      <td>3</td>\n",
       "      <td>1</td>\n",
       "      <td>21</td>\n",
       "      <td>0</td>\n",
       "      <td>4</td>\n",
       "      <td>0</td>\n",
       "      <td>8</td>\n",
       "      <td>4</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>8</th>\n",
       "      <td>1</td>\n",
       "      <td>3</td>\n",
       "      <td>1</td>\n",
       "      <td>3</td>\n",
       "      <td>0</td>\n",
       "      <td>2</td>\n",
       "      <td>11</td>\n",
       "      <td>0</td>\n",
       "      <td>2</td>\n",
       "      <td>0</td>\n",
       "      <td>8</td>\n",
       "      <td>3</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>9</th>\n",
       "      <td>1</td>\n",
       "      <td>2</td>\n",
       "      <td>1</td>\n",
       "      <td>1</td>\n",
       "      <td>1</td>\n",
       "      <td>0</td>\n",
       "      <td>30</td>\n",
       "      <td>1</td>\n",
       "      <td>1</td>\n",
       "      <td>0</td>\n",
       "      <td>8</td>\n",
       "      <td>3</td>\n",
       "    </tr>\n",
       "  </tbody>\n",
       "</table>\n",
       "</div>"
      ],
      "text/plain": [
       "   Survived  Pclass  Sex  Age  SibSp  Parch  Fare  Embarked  relatives  \\\n",
       "0         0       3    0    2      1      0     7         0          1   \n",
       "1         1       1    1    5      1      0    71         1          1   \n",
       "2         1       3    1    3      0      0     7         0          0   \n",
       "3         1       1    1    5      1      0    53         0          1   \n",
       "4         0       3    0    5      0      0     8         0          0   \n",
       "5         0       3    0    4      0      0     8         2          0   \n",
       "6         0       1    0    6      0      0    51         0          0   \n",
       "7         0       3    0    0      3      1    21         0          4   \n",
       "8         1       3    1    3      0      2    11         0          2   \n",
       "9         1       2    1    1      1      0    30         1          1   \n",
       "\n",
       "   not_alone  Deck  Title  \n",
       "0          0     8      1  \n",
       "1          0     3      3  \n",
       "2          1     8      2  \n",
       "3          0     3      3  \n",
       "4          1     8      1  \n",
       "5          1     8      1  \n",
       "6          1     5      1  \n",
       "7          0     8      4  \n",
       "8          0     8      3  \n",
       "9          0     8      3  "
      ]
     },
     "execution_count": 253,
     "metadata": {},
     "output_type": "execute_result"
    }
   ],
   "source": [
    "train_df.head(10)"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 254,
   "metadata": {},
   "outputs": [],
   "source": [
    "data = [train_df, test_df]\n",
    "\n",
    "for dataset in data:\n",
    "    dataset.loc[ dataset['Fare'] <= 7.91, 'Fare'] = 0\n",
    "    dataset.loc[(dataset['Fare'] > 7.91) & (dataset['Fare'] <= 14.454), 'Fare'] = 1\n",
    "    dataset.loc[(dataset['Fare'] > 14.454) & (dataset['Fare'] <= 31), 'Fare']   = 2\n",
    "    dataset.loc[(dataset['Fare'] > 31) & (dataset['Fare'] <= 99), 'Fare']   = 3\n",
    "    dataset.loc[(dataset['Fare'] > 99) & (dataset['Fare'] <= 250), 'Fare']   = 4\n",
    "    dataset.loc[ dataset['Fare'] > 250, 'Fare'] = 5\n",
    "    dataset['Fare'] = dataset['Fare'].astype(int)"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 255,
   "metadata": {},
   "outputs": [
    {
     "data": {
      "text/plain": [
       "0    241\n",
       "2    223\n",
       "1    216\n",
       "3    158\n",
       "4     44\n",
       "5      9\n",
       "Name: Fare, dtype: int64"
      ]
     },
     "execution_count": 255,
     "metadata": {},
     "output_type": "execute_result"
    }
   ],
   "source": [
    "train_df['Fare'].value_counts()"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "# Creating new Features\n",
    "\n",
    "I will add two new features to the dataset, that I compute out of other features.\n",
    "\n",
    "### 1. Age times Class"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 256,
   "metadata": {},
   "outputs": [],
   "source": [
    "data = [train_df, test_df]\n",
    "for dataset in data:\n",
    "    dataset['Age_Class']= dataset['Age']* dataset['Pclass']"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "### 2.  Fare per Person"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 257,
   "metadata": {},
   "outputs": [],
   "source": [
    "for dataset in data:\n",
    "    dataset['Fare_Per_Person'] = dataset['Fare']/(dataset['relatives']+1)\n",
    "    dataset['Fare_Per_Person'] = dataset['Fare_Per_Person'].astype(int)"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 258,
   "metadata": {},
   "outputs": [
    {
     "data": {
      "text/html": [
       "<div>\n",
       "<style scoped>\n",
       "    .dataframe tbody tr th:only-of-type {\n",
       "        vertical-align: middle;\n",
       "    }\n",
       "\n",
       "    .dataframe tbody tr th {\n",
       "        vertical-align: top;\n",
       "    }\n",
       "\n",
       "    .dataframe thead th {\n",
       "        text-align: right;\n",
       "    }\n",
       "</style>\n",
       "<table border=\"1\" class=\"dataframe\">\n",
       "  <thead>\n",
       "    <tr style=\"text-align: right;\">\n",
       "      <th></th>\n",
       "      <th>Survived</th>\n",
       "      <th>Pclass</th>\n",
       "      <th>Sex</th>\n",
       "      <th>Age</th>\n",
       "      <th>SibSp</th>\n",
       "      <th>Parch</th>\n",
       "      <th>Fare</th>\n",
       "      <th>Embarked</th>\n",
       "      <th>relatives</th>\n",
       "      <th>not_alone</th>\n",
       "      <th>Deck</th>\n",
       "      <th>Title</th>\n",
       "      <th>Age_Class</th>\n",
       "      <th>Fare_Per_Person</th>\n",
       "    </tr>\n",
       "  </thead>\n",
       "  <tbody>\n",
       "    <tr>\n",
       "      <th>0</th>\n",
       "      <td>0</td>\n",
       "      <td>3</td>\n",
       "      <td>0</td>\n",
       "      <td>2</td>\n",
       "      <td>1</td>\n",
       "      <td>0</td>\n",
       "      <td>0</td>\n",
       "      <td>0</td>\n",
       "      <td>1</td>\n",
       "      <td>0</td>\n",
       "      <td>8</td>\n",
       "      <td>1</td>\n",
       "      <td>6</td>\n",
       "      <td>0</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>1</th>\n",
       "      <td>1</td>\n",
       "      <td>1</td>\n",
       "      <td>1</td>\n",
       "      <td>5</td>\n",
       "      <td>1</td>\n",
       "      <td>0</td>\n",
       "      <td>3</td>\n",
       "      <td>1</td>\n",
       "      <td>1</td>\n",
       "      <td>0</td>\n",
       "      <td>3</td>\n",
       "      <td>3</td>\n",
       "      <td>5</td>\n",
       "      <td>1</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>2</th>\n",
       "      <td>1</td>\n",
       "      <td>3</td>\n",
       "      <td>1</td>\n",
       "      <td>3</td>\n",
       "      <td>0</td>\n",
       "      <td>0</td>\n",
       "      <td>0</td>\n",
       "      <td>0</td>\n",
       "      <td>0</td>\n",
       "      <td>1</td>\n",
       "      <td>8</td>\n",
       "      <td>2</td>\n",
       "      <td>9</td>\n",
       "      <td>0</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>3</th>\n",
       "      <td>1</td>\n",
       "      <td>1</td>\n",
       "      <td>1</td>\n",
       "      <td>5</td>\n",
       "      <td>1</td>\n",
       "      <td>0</td>\n",
       "      <td>3</td>\n",
       "      <td>0</td>\n",
       "      <td>1</td>\n",
       "      <td>0</td>\n",
       "      <td>3</td>\n",
       "      <td>3</td>\n",
       "      <td>5</td>\n",
       "      <td>1</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>4</th>\n",
       "      <td>0</td>\n",
       "      <td>3</td>\n",
       "      <td>0</td>\n",
       "      <td>5</td>\n",
       "      <td>0</td>\n",
       "      <td>0</td>\n",
       "      <td>1</td>\n",
       "      <td>0</td>\n",
       "      <td>0</td>\n",
       "      <td>1</td>\n",
       "      <td>8</td>\n",
       "      <td>1</td>\n",
       "      <td>15</td>\n",
       "      <td>1</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>5</th>\n",
       "      <td>0</td>\n",
       "      <td>3</td>\n",
       "      <td>0</td>\n",
       "      <td>4</td>\n",
       "      <td>0</td>\n",
       "      <td>0</td>\n",
       "      <td>1</td>\n",
       "      <td>2</td>\n",
       "      <td>0</td>\n",
       "      <td>1</td>\n",
       "      <td>8</td>\n",
       "      <td>1</td>\n",
       "      <td>12</td>\n",
       "      <td>1</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>6</th>\n",
       "      <td>0</td>\n",
       "      <td>1</td>\n",
       "      <td>0</td>\n",
       "      <td>6</td>\n",
       "      <td>0</td>\n",
       "      <td>0</td>\n",
       "      <td>3</td>\n",
       "      <td>0</td>\n",
       "      <td>0</td>\n",
       "      <td>1</td>\n",
       "      <td>5</td>\n",
       "      <td>1</td>\n",
       "      <td>6</td>\n",
       "      <td>3</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>7</th>\n",
       "      <td>0</td>\n",
       "      <td>3</td>\n",
       "      <td>0</td>\n",
       "      <td>0</td>\n",
       "      <td>3</td>\n",
       "      <td>1</td>\n",
       "      <td>2</td>\n",
       "      <td>0</td>\n",
       "      <td>4</td>\n",
       "      <td>0</td>\n",
       "      <td>8</td>\n",
       "      <td>4</td>\n",
       "      <td>0</td>\n",
       "      <td>0</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>8</th>\n",
       "      <td>1</td>\n",
       "      <td>3</td>\n",
       "      <td>1</td>\n",
       "      <td>3</td>\n",
       "      <td>0</td>\n",
       "      <td>2</td>\n",
       "      <td>1</td>\n",
       "      <td>0</td>\n",
       "      <td>2</td>\n",
       "      <td>0</td>\n",
       "      <td>8</td>\n",
       "      <td>3</td>\n",
       "      <td>9</td>\n",
       "      <td>0</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>9</th>\n",
       "      <td>1</td>\n",
       "      <td>2</td>\n",
       "      <td>1</td>\n",
       "      <td>1</td>\n",
       "      <td>1</td>\n",
       "      <td>0</td>\n",
       "      <td>2</td>\n",
       "      <td>1</td>\n",
       "      <td>1</td>\n",
       "      <td>0</td>\n",
       "      <td>8</td>\n",
       "      <td>3</td>\n",
       "      <td>2</td>\n",
       "      <td>1</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>10</th>\n",
       "      <td>1</td>\n",
       "      <td>3</td>\n",
       "      <td>1</td>\n",
       "      <td>0</td>\n",
       "      <td>1</td>\n",
       "      <td>1</td>\n",
       "      <td>2</td>\n",
       "      <td>0</td>\n",
       "      <td>2</td>\n",
       "      <td>0</td>\n",
       "      <td>7</td>\n",
       "      <td>2</td>\n",
       "      <td>0</td>\n",
       "      <td>0</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>11</th>\n",
       "      <td>1</td>\n",
       "      <td>1</td>\n",
       "      <td>1</td>\n",
       "      <td>6</td>\n",
       "      <td>0</td>\n",
       "      <td>0</td>\n",
       "      <td>2</td>\n",
       "      <td>0</td>\n",
       "      <td>0</td>\n",
       "      <td>1</td>\n",
       "      <td>3</td>\n",
       "      <td>2</td>\n",
       "      <td>6</td>\n",
       "      <td>2</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>12</th>\n",
       "      <td>0</td>\n",
       "      <td>3</td>\n",
       "      <td>0</td>\n",
       "      <td>2</td>\n",
       "      <td>0</td>\n",
       "      <td>0</td>\n",
       "      <td>1</td>\n",
       "      <td>0</td>\n",
       "      <td>0</td>\n",
       "      <td>1</td>\n",
       "      <td>8</td>\n",
       "      <td>1</td>\n",
       "      <td>6</td>\n",
       "      <td>1</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>13</th>\n",
       "      <td>0</td>\n",
       "      <td>3</td>\n",
       "      <td>0</td>\n",
       "      <td>5</td>\n",
       "      <td>1</td>\n",
       "      <td>5</td>\n",
       "      <td>2</td>\n",
       "      <td>0</td>\n",
       "      <td>6</td>\n",
       "      <td>0</td>\n",
       "      <td>8</td>\n",
       "      <td>1</td>\n",
       "      <td>15</td>\n",
       "      <td>0</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>14</th>\n",
       "      <td>0</td>\n",
       "      <td>3</td>\n",
       "      <td>1</td>\n",
       "      <td>1</td>\n",
       "      <td>0</td>\n",
       "      <td>0</td>\n",
       "      <td>0</td>\n",
       "      <td>0</td>\n",
       "      <td>0</td>\n",
       "      <td>1</td>\n",
       "      <td>8</td>\n",
       "      <td>2</td>\n",
       "      <td>3</td>\n",
       "      <td>0</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>15</th>\n",
       "      <td>1</td>\n",
       "      <td>2</td>\n",
       "      <td>1</td>\n",
       "      <td>6</td>\n",
       "      <td>0</td>\n",
       "      <td>0</td>\n",
       "      <td>2</td>\n",
       "      <td>0</td>\n",
       "      <td>0</td>\n",
       "      <td>1</td>\n",
       "      <td>8</td>\n",
       "      <td>3</td>\n",
       "      <td>12</td>\n",
       "      <td>2</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>16</th>\n",
       "      <td>0</td>\n",
       "      <td>3</td>\n",
       "      <td>0</td>\n",
       "      <td>0</td>\n",
       "      <td>4</td>\n",
       "      <td>1</td>\n",
       "      <td>2</td>\n",
       "      <td>2</td>\n",
       "      <td>5</td>\n",
       "      <td>0</td>\n",
       "      <td>8</td>\n",
       "      <td>4</td>\n",
       "      <td>0</td>\n",
       "      <td>0</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>17</th>\n",
       "      <td>1</td>\n",
       "      <td>2</td>\n",
       "      <td>0</td>\n",
       "      <td>4</td>\n",
       "      <td>0</td>\n",
       "      <td>0</td>\n",
       "      <td>1</td>\n",
       "      <td>0</td>\n",
       "      <td>0</td>\n",
       "      <td>1</td>\n",
       "      <td>8</td>\n",
       "      <td>1</td>\n",
       "      <td>8</td>\n",
       "      <td>1</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>18</th>\n",
       "      <td>0</td>\n",
       "      <td>3</td>\n",
       "      <td>1</td>\n",
       "      <td>4</td>\n",
       "      <td>1</td>\n",
       "      <td>0</td>\n",
       "      <td>2</td>\n",
       "      <td>0</td>\n",
       "      <td>1</td>\n",
       "      <td>0</td>\n",
       "      <td>8</td>\n",
       "      <td>3</td>\n",
       "      <td>12</td>\n",
       "      <td>1</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>19</th>\n",
       "      <td>1</td>\n",
       "      <td>3</td>\n",
       "      <td>1</td>\n",
       "      <td>3</td>\n",
       "      <td>0</td>\n",
       "      <td>0</td>\n",
       "      <td>0</td>\n",
       "      <td>1</td>\n",
       "      <td>0</td>\n",
       "      <td>1</td>\n",
       "      <td>8</td>\n",
       "      <td>3</td>\n",
       "      <td>9</td>\n",
       "      <td>0</td>\n",
       "    </tr>\n",
       "  </tbody>\n",
       "</table>\n",
       "</div>"
      ],
      "text/plain": [
       "    Survived  Pclass  Sex  Age  SibSp  Parch  Fare  Embarked  relatives  \\\n",
       "0          0       3    0    2      1      0     0         0          1   \n",
       "1          1       1    1    5      1      0     3         1          1   \n",
       "2          1       3    1    3      0      0     0         0          0   \n",
       "3          1       1    1    5      1      0     3         0          1   \n",
       "4          0       3    0    5      0      0     1         0          0   \n",
       "5          0       3    0    4      0      0     1         2          0   \n",
       "6          0       1    0    6      0      0     3         0          0   \n",
       "7          0       3    0    0      3      1     2         0          4   \n",
       "8          1       3    1    3      0      2     1         0          2   \n",
       "9          1       2    1    1      1      0     2         1          1   \n",
       "10         1       3    1    0      1      1     2         0          2   \n",
       "11         1       1    1    6      0      0     2         0          0   \n",
       "12         0       3    0    2      0      0     1         0          0   \n",
       "13         0       3    0    5      1      5     2         0          6   \n",
       "14         0       3    1    1      0      0     0         0          0   \n",
       "15         1       2    1    6      0      0     2         0          0   \n",
       "16         0       3    0    0      4      1     2         2          5   \n",
       "17         1       2    0    4      0      0     1         0          0   \n",
       "18         0       3    1    4      1      0     2         0          1   \n",
       "19         1       3    1    3      0      0     0         1          0   \n",
       "\n",
       "    not_alone  Deck  Title  Age_Class  Fare_Per_Person  \n",
       "0           0     8      1          6                0  \n",
       "1           0     3      3          5                1  \n",
       "2           1     8      2          9                0  \n",
       "3           0     3      3          5                1  \n",
       "4           1     8      1         15                1  \n",
       "5           1     8      1         12                1  \n",
       "6           1     5      1          6                3  \n",
       "7           0     8      4          0                0  \n",
       "8           0     8      3          9                0  \n",
       "9           0     8      3          2                1  \n",
       "10          0     7      2          0                0  \n",
       "11          1     3      2          6                2  \n",
       "12          1     8      1          6                1  \n",
       "13          0     8      1         15                0  \n",
       "14          1     8      2          3                0  \n",
       "15          1     8      3         12                2  \n",
       "16          0     8      4          0                0  \n",
       "17          1     8      1          8                1  \n",
       "18          0     8      3         12                1  \n",
       "19          1     8      3          9                0  "
      ]
     },
     "execution_count": 258,
     "metadata": {},
     "output_type": "execute_result"
    }
   ],
   "source": [
    "# Let's take a last look at the training set, before we start training the models.\n",
    "train_df.head(20)"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "# **Building Machine Learning Models**"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 259,
   "metadata": {},
   "outputs": [],
   "source": [
    "X_train = train_df.drop(\"Survived\", axis=1)\n",
    "Y_train = train_df[\"Survived\"]\n",
    "X_test  = test_df.drop(\"PassengerId\", axis=1).copy()"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 260,
   "metadata": {},
   "outputs": [
    {
     "name": "stdout",
     "output_type": "stream",
     "text": [
      "<class 'pandas.core.frame.DataFrame'>\n",
      "RangeIndex: 891 entries, 0 to 890\n",
      "Data columns (total 14 columns):\n",
      "Survived           891 non-null int64\n",
      "Pclass             891 non-null int64\n",
      "Sex                891 non-null int64\n",
      "Age                891 non-null int32\n",
      "SibSp              891 non-null int64\n",
      "Parch              891 non-null int64\n",
      "Fare               891 non-null int32\n",
      "Embarked           891 non-null int64\n",
      "relatives          891 non-null int64\n",
      "not_alone          891 non-null int32\n",
      "Deck               891 non-null int32\n",
      "Title              891 non-null int64\n",
      "Age_Class          891 non-null int64\n",
      "Fare_Per_Person    891 non-null int32\n",
      "dtypes: int32(5), int64(9)\n",
      "memory usage: 80.1 KB\n"
     ]
    }
   ],
   "source": [
    "train_df.info()"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 266,
   "metadata": {},
   "outputs": [
    {
     "name": "stdout",
     "output_type": "stream",
     "text": [
      "78.79 %\n"
     ]
    }
   ],
   "source": [
    "# stochastic gradient descent (SGD) learning\n",
    "sgd = linear_model.SGDClassifier(max_iter=5, tol=None)\n",
    "sgd.fit(X_train, Y_train)\n",
    "Y_pred = sgd.predict(X_test)\n",
    "\n",
    "sgd.score(X_train, Y_train)\n",
    "\n",
    "acc_sgd = round(sgd.score(X_train, Y_train) * 100, 2)\n",
    "\n",
    "\n",
    "print(round(acc_sgd,2,), \"%\")"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 268,
   "metadata": {},
   "outputs": [
    {
     "name": "stdout",
     "output_type": "stream",
     "text": [
      "92.82 %\n"
     ]
    }
   ],
   "source": [
    "# Random Forest\n",
    "random_forest = RandomForestClassifier(n_estimators=100)\n",
    "random_forest.fit(X_train, Y_train)\n",
    "\n",
    "Y_prediction = random_forest.predict(X_test)\n",
    "\n",
    "random_forest.score(X_train, Y_train)\n",
    "acc_random_forest = round(random_forest.score(X_train, Y_train) * 100, 2)\n",
    "print(round(acc_random_forest,2,), \"%\")"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 270,
   "metadata": {},
   "outputs": [
    {
     "name": "stdout",
     "output_type": "stream",
     "text": [
      "81.93 %\n"
     ]
    }
   ],
   "source": [
    "# Logistic Regression\n",
    "logreg = LogisticRegression()\n",
    "logreg.fit(X_train, Y_train)\n",
    "\n",
    "Y_pred = logreg.predict(X_test)\n",
    "\n",
    "acc_log = round(logreg.score(X_train, Y_train) * 100, 2)\n",
    "print(round(acc_log,2,), \"%\")"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 272,
   "metadata": {},
   "outputs": [
    {
     "name": "stdout",
     "output_type": "stream",
     "text": [
      "86.64 %\n"
     ]
    }
   ],
   "source": [
    "# KNN\n",
    "knn = KNeighborsClassifier(n_neighbors = 3)\n",
    "knn.fit(X_train, Y_train)\n",
    "\n",
    "Y_pred = knn.predict(X_test)\n",
    "\n",
    "acc_knn = round(knn.score(X_train, Y_train) * 100, 2)\n",
    "print(round(acc_knn,2,), \"%\")"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 273,
   "metadata": {},
   "outputs": [
    {
     "name": "stdout",
     "output_type": "stream",
     "text": [
      "77.89 %\n"
     ]
    }
   ],
   "source": [
    "# Gaussian Naive Bayes\n",
    "gaussian = GaussianNB()\n",
    "gaussian.fit(X_train, Y_train)\n",
    "\n",
    "Y_pred = gaussian.predict(X_test)\n",
    "\n",
    "acc_gaussian = round(gaussian.score(X_train, Y_train) * 100, 2)\n",
    "print(round(acc_gaussian,2,), \"%\")"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 274,
   "metadata": {},
   "outputs": [
    {
     "name": "stdout",
     "output_type": "stream",
     "text": [
      "81.48 %\n"
     ]
    }
   ],
   "source": [
    "# Perceptron\n",
    "perceptron = Perceptron(max_iter=5)\n",
    "perceptron.fit(X_train, Y_train)\n",
    "\n",
    "Y_pred = perceptron.predict(X_test)\n",
    "\n",
    "acc_perceptron = round(perceptron.score(X_train, Y_train) * 100, 2)\n",
    "print(round(acc_perceptron,2,), \"%\")"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 276,
   "metadata": {},
   "outputs": [
    {
     "name": "stdout",
     "output_type": "stream",
     "text": [
      "81.71 %\n"
     ]
    }
   ],
   "source": [
    "# Linear SVC\n",
    "linear_svc = LinearSVC()\n",
    "linear_svc.fit(X_train, Y_train)\n",
    "\n",
    "Y_pred = linear_svc.predict(X_test)\n",
    "\n",
    "acc_linear_svc = round(linear_svc.score(X_train, Y_train) * 100, 2)\n",
    "print(round(acc_linear_svc,2,), \"%\")"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 278,
   "metadata": {},
   "outputs": [
    {
     "name": "stdout",
     "output_type": "stream",
     "text": [
      "92.93 %\n"
     ]
    }
   ],
   "source": [
    "# Decision Tree\n",
    "decision_tree = DecisionTreeClassifier()\n",
    "decision_tree.fit(X_train, Y_train)\n",
    "\n",
    "Y_pred = decision_tree.predict(X_test)\n",
    "\n",
    "acc_decision_tree = round(decision_tree.score(X_train, Y_train) * 100, 2)\n",
    "print(round(acc_decision_tree,2,), \"%\")"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "### Which is the best Model"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 280,
   "metadata": {},
   "outputs": [
    {
     "data": {
      "text/html": [
       "<div>\n",
       "<style scoped>\n",
       "    .dataframe tbody tr th:only-of-type {\n",
       "        vertical-align: middle;\n",
       "    }\n",
       "\n",
       "    .dataframe tbody tr th {\n",
       "        vertical-align: top;\n",
       "    }\n",
       "\n",
       "    .dataframe thead th {\n",
       "        text-align: right;\n",
       "    }\n",
       "</style>\n",
       "<table border=\"1\" class=\"dataframe\">\n",
       "  <thead>\n",
       "    <tr style=\"text-align: right;\">\n",
       "      <th></th>\n",
       "      <th>Model</th>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>Score</th>\n",
       "      <th></th>\n",
       "    </tr>\n",
       "  </thead>\n",
       "  <tbody>\n",
       "    <tr>\n",
       "      <th>92.93</th>\n",
       "      <td>Decision Tree</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>92.82</th>\n",
       "      <td>Random Forest</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>86.64</th>\n",
       "      <td>KNN</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>81.93</th>\n",
       "      <td>Logistic Regression</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>81.71</th>\n",
       "      <td>Support Vector Machines</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>81.48</th>\n",
       "      <td>Perceptron</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>78.79</th>\n",
       "      <td>Stochastic Gradient Decent</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>77.89</th>\n",
       "      <td>Naive Bayes</td>\n",
       "    </tr>\n",
       "  </tbody>\n",
       "</table>\n",
       "</div>"
      ],
      "text/plain": [
       "                            Model\n",
       "Score                            \n",
       "92.93               Decision Tree\n",
       "92.82               Random Forest\n",
       "86.64                         KNN\n",
       "81.93         Logistic Regression\n",
       "81.71     Support Vector Machines\n",
       "81.48                  Perceptron\n",
       "78.79  Stochastic Gradient Decent\n",
       "77.89                 Naive Bayes"
      ]
     },
     "execution_count": 280,
     "metadata": {},
     "output_type": "execute_result"
    }
   ],
   "source": [
    "results = pd.DataFrame({\n",
    "    'Model': ['Support Vector Machines', 'KNN', 'Logistic Regression', \n",
    "              'Random Forest', 'Naive Bayes', 'Perceptron', \n",
    "              'Stochastic Gradient Decent', \n",
    "              'Decision Tree'],\n",
    "    'Score': [acc_linear_svc, acc_knn, acc_log, \n",
    "              acc_random_forest, acc_gaussian, acc_perceptron, \n",
    "              acc_sgd, acc_decision_tree]})\n",
    "result_df = results.sort_values(by='Score', ascending=False)\n",
    "result_df = result_df.set_index('Score')\n",
    "result_df.head(9)"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "As we can see, the Random Forest classifier goes on the first place. But first, let us check, how random-forest performs, when we use cross validation. "
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "## K-Fold Cross Validation:\n",
    "\n",
    "K-Fold Cross Validation randomly splits the training data into **K subsets called folds**. Let's image we would split our data into 4 folds (K = 4).  Our random forest model would be trained and evaluated 4 times, using a different fold for evaluation everytime, while it would be trained on the remaining 3 folds. \n",
    "\n",
    "The image below shows the process, using 4 folds (K = 4). Every row represents one training + evaluation process. In the first row, the model get's trained on the first, second and third subset and evaluated on the fourth. In the second row, the model get's trained on the second, third and fourth subset and evaluated on the first. K-Fold Cross Validation repeats this process till every fold acted once as an evaluation fold.\n",
    "\n",
    "\n",
    "The result of our K-Fold Cross Validation example would be an array that contains 4 different scores. We then need to compute the mean and the standard deviation for these scores. \n",
    "\n",
    "The code below perform K-Fold Cross Validation on our random forest model, using 10 folds (K = 10). Therefore it outputs an array with 10 different scores.\n"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 283,
   "metadata": {},
   "outputs": [],
   "source": [
    "from sklearn.model_selection import cross_val_score\n",
    "rf = RandomForestClassifier(n_estimators=100)\n",
    "scores = cross_val_score(rf, X_train, Y_train, cv=10, scoring = \"accuracy\")"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 285,
   "metadata": {},
   "outputs": [
    {
     "name": "stdout",
     "output_type": "stream",
     "text": [
      "Scores: [0.75555556 0.82222222 0.75280899 0.86516854 0.91011236 0.85393258\n",
      " 0.82022472 0.78651685 0.84269663 0.82954545]\n",
      "Mean: 0.8238783906480537\n",
      "Standard Deviation: 0.046434379887403315\n"
     ]
    }
   ],
   "source": [
    "print(\"Scores:\", scores)\n",
    "print(\"Mean:\", scores.mean())\n",
    "print(\"Standard Deviation:\", scores.std())"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "This looks much more realistic than before.  Our model has a average accuracy of 82% with a standard deviation of 4 %. The standard deviation shows us, how precise the estimates are . \n",
    "\n",
    "This means in our case that the accuracy of our model can differ **+ -** 4%.\n",
    "\n",
    "I think the accuracy is still really good and since random forest is an easy to use model, we will try to increase it's performance even further in the following section."
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 288,
   "metadata": {},
   "outputs": [],
   "source": [
    "importances = pd.DataFrame({'feature':X_train.columns,'importance':np.round(random_forest.feature_importances_,3)})\n",
    "importances = importances.sort_values('importance',ascending=False).set_index('feature')"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 290,
   "metadata": {},
   "outputs": [
    {
     "data": {
      "text/html": [
       "<div>\n",
       "<style scoped>\n",
       "    .dataframe tbody tr th:only-of-type {\n",
       "        vertical-align: middle;\n",
       "    }\n",
       "\n",
       "    .dataframe tbody tr th {\n",
       "        vertical-align: top;\n",
       "    }\n",
       "\n",
       "    .dataframe thead th {\n",
       "        text-align: right;\n",
       "    }\n",
       "</style>\n",
       "<table border=\"1\" class=\"dataframe\">\n",
       "  <thead>\n",
       "    <tr style=\"text-align: right;\">\n",
       "      <th></th>\n",
       "      <th>importance</th>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>feature</th>\n",
       "      <th></th>\n",
       "    </tr>\n",
       "  </thead>\n",
       "  <tbody>\n",
       "    <tr>\n",
       "      <th>Title</th>\n",
       "      <td>0.207</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>Sex</th>\n",
       "      <td>0.167</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>Age_Class</th>\n",
       "      <td>0.099</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>Deck</th>\n",
       "      <td>0.083</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>Pclass</th>\n",
       "      <td>0.076</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>Age</th>\n",
       "      <td>0.075</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>Fare</th>\n",
       "      <td>0.065</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>relatives</th>\n",
       "      <td>0.058</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>Embarked</th>\n",
       "      <td>0.051</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>Fare_Per_Person</th>\n",
       "      <td>0.045</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>SibSp</th>\n",
       "      <td>0.037</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>Parch</th>\n",
       "      <td>0.022</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>not_alone</th>\n",
       "      <td>0.014</td>\n",
       "    </tr>\n",
       "  </tbody>\n",
       "</table>\n",
       "</div>"
      ],
      "text/plain": [
       "                 importance\n",
       "feature                    \n",
       "Title                 0.207\n",
       "Sex                   0.167\n",
       "Age_Class             0.099\n",
       "Deck                  0.083\n",
       "Pclass                0.076\n",
       "Age                   0.075\n",
       "Fare                  0.065\n",
       "relatives             0.058\n",
       "Embarked              0.051\n",
       "Fare_Per_Person       0.045\n",
       "SibSp                 0.037\n",
       "Parch                 0.022\n",
       "not_alone             0.014"
      ]
     },
     "execution_count": 290,
     "metadata": {},
     "output_type": "execute_result"
    }
   ],
   "source": [
    "importances.head(15)"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 291,
   "metadata": {},
   "outputs": [
    {
     "data": {
      "text/plain": [
       "<matplotlib.axes._subplots.AxesSubplot at 0x20c86b64278>"
      ]
     },
     "execution_count": 291,
     "metadata": {},
     "output_type": "execute_result"
    },
    {
     "data": {
      "image/png": "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\n",
      "text/plain": [
       "<matplotlib.figure.Figure at 0x20c86b5ef28>"
      ]
     },
     "metadata": {},
     "output_type": "display_data"
    }
   ],
   "source": [
    "importances.plot.bar()"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "**Conclusion:**\n",
    "\n",
    "not_alone and Parch doesn't play a significant role in our random forest classifiers prediction process. Because of that I will drop them from the dataset and train the classifier again. We could also remove more or less features, but this would need a more detailed investigation of the features effect on our model. But I think it's just fine to remove only Alone and Parch."
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 292,
   "metadata": {},
   "outputs": [],
   "source": [
    "train_df  = train_df.drop(\"not_alone\", axis=1)\n",
    "test_df  = test_df.drop(\"not_alone\", axis=1)\n",
    "\n",
    "train_df  = train_df.drop(\"Parch\", axis=1)\n",
    "test_df  = test_df.drop(\"Parch\", axis=1)"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 294,
   "metadata": {},
   "outputs": [
    {
     "name": "stdout",
     "output_type": "stream",
     "text": [
      "92.93 %\n"
     ]
    }
   ],
   "source": [
    "# Random Forest\n",
    "\n",
    "random_forest = RandomForestClassifier(n_estimators=100, oob_score = True)\n",
    "random_forest.fit(X_train, Y_train)\n",
    "Y_prediction = random_forest.predict(X_test)\n",
    "\n",
    "random_forest.score(X_train, Y_train)\n",
    "\n",
    "acc_random_forest = round(random_forest.score(X_train, Y_train) * 100, 2)\n",
    "print(round(acc_random_forest,2,), \"%\")"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "Our random forest model predicts as good as it did before. A general rule is that,  **the more features you have, the more likely your model will suffer from overfitting** and vice versa. But I think our data looks fine for now and hasn't too much features.\n",
    "\n",
    "There is also another way to evaluate a random-forest classifier, which is probably much more accurate than the score we used before. What I am talking about is the **out-of-bag samples** to estimate the generalization accuracy. I will not go into details here about how it works. Just note that out-of-bag estimate is as accurate as using a test set of the same size as the training set. Therefore, using the out-of-bag error estimate removes the need for a set aside test set."
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 296,
   "metadata": {},
   "outputs": [
    {
     "name": "stdout",
     "output_type": "stream",
     "text": [
      "oob score: 81.82000000000001 %\n"
     ]
    }
   ],
   "source": [
    "print(\"oob score:\", round(random_forest.oob_score_, 4)*100, \"%\")"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "Now we can start tuning the hyperameters of random forest. "
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 299,
   "metadata": {},
   "outputs": [
    {
     "name": "stdout",
     "output_type": "stream",
     "text": [
      "oob score: 83.84 %\n"
     ]
    }
   ],
   "source": [
    "# Random Forest\n",
    "random_forest = RandomForestClassifier(criterion = \"gini\", \n",
    "                                       min_samples_leaf = 1, \n",
    "                                       min_samples_split = 10,   \n",
    "                                       n_estimators=100, \n",
    "                                       max_features='auto', \n",
    "                                       oob_score=True, \n",
    "                                       random_state=1, \n",
    "                                       n_jobs=-1)\n",
    "\n",
    "random_forest.fit(X_train, Y_train)\n",
    "Y_prediction = random_forest.predict(X_test)\n",
    "\n",
    "random_forest.score(X_train, Y_train)\n",
    "\n",
    "print(\"oob score:\", round(random_forest.oob_score_, 4)*100, \"%\")"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "Now that we have a proper model, we can start evaluating it's performace in a more accurate way. Previously we only used accuracy and the oob score, which is just another form of accuracy. The problem is just, that it's more complicated to evaluate a classification model than a regression model. We will talk about this in the following section."
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "# **Further Evaluation**"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "## Confusion Matrix:"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 304,
   "metadata": {},
   "outputs": [
    {
     "data": {
      "text/plain": [
       "array([[494,  55],\n",
       "       [ 95, 247]], dtype=int64)"
      ]
     },
     "execution_count": 304,
     "metadata": {},
     "output_type": "execute_result"
    }
   ],
   "source": [
    "from sklearn.model_selection import cross_val_predict\n",
    "from sklearn.metrics import confusion_matrix\n",
    "predictions = cross_val_predict(random_forest, X_train, Y_train, cv=3)\n",
    "confusion_matrix(Y_train, predictions)"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "The first row is about the not-survived-predictions: **494 passengers were correctly classified as not survived** (called true negatives)  and **55 where wrongly classified as not survived** (false negatives).\n",
    "\n",
    "The second row is about the survived-predictions: **95 passengers where wrongly classified as survived** (false positives) and **247 where correctly classified as survived** (true positives).\n",
    "\n",
    "A confusion matrix gives you a lot of information about how well your model does, but theres a way to get even more, like computing the classifiers precision."
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "## Precision and Recall:"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 306,
   "metadata": {},
   "outputs": [
    {
     "name": "stdout",
     "output_type": "stream",
     "text": [
      "Precision: 0.8178807947019867\n",
      "Recall: 0.7222222222222222\n"
     ]
    }
   ],
   "source": [
    "from sklearn.metrics import precision_score, recall_score\n",
    "\n",
    "print(\"Precision:\", precision_score(Y_train, predictions))\n",
    "print(\"Recall:\",recall_score(Y_train, predictions))"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "Our model predicts 81% of the time, a passengers survival correctly (precision). The recall tells us that it predicted the survival of 73 % of the people who actually survived. "
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "## F-Score\n",
    "\n",
    "You can combine precision and recall into one score, which is called the F-score. The F-score is computed with the harmonic mean of precision and recall. Note that it assigns much more weight to low values. As a result of that, the classifier will only get a high F-score, if both recall and precision are high."
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 308,
   "metadata": {},
   "outputs": [
    {
     "data": {
      "text/plain": [
       "0.767080745341615"
      ]
     },
     "execution_count": 308,
     "metadata": {},
     "output_type": "execute_result"
    }
   ],
   "source": [
    "from sklearn.metrics import f1_score\n",
    "f1_score(Y_train, predictions)"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "## Precision Recall Curve\n",
    "\n",
    "For each person the Random Forest algorithm has to classify, it computes a probability based on a function and it classifies the person as survived (when the score is bigger the than threshold) or as not survived (when the score is smaller than the threshold). That's why the threshold plays an important part.\n",
    "\n",
    "We will plot the precision and recall with the threshold using matplotlib:"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 311,
   "metadata": {},
   "outputs": [],
   "source": [
    "from sklearn.metrics import precision_recall_curve\n",
    "\n",
    "# getting the probabilities of our predictions\n",
    "y_scores = random_forest.predict_proba(X_train)\n",
    "y_scores = y_scores[:,1]\n",
    "\n",
    "precision, recall, threshold = precision_recall_curve(Y_train, y_scores)"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 314,
   "metadata": {},
   "outputs": [
    {
     "data": {
      "image/png": "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ABGRaOO2zuqpp+xxOBIdlFjl1HffubcvXmyPOC9c2NNwREQkfPTq5d7eu7c9Z1AinxKrnNq2zb29fn1v4xARkbDTqhU89JBvuzFw442wapX3MYm3lFjlxL//2kOVM+va1ftYREQkLA0cCN27+7YfOKCDN6KBEqtApKXBM89A5cqwYoXv/VNO8T4mEREJS45jD+K45BLfe5Mmwddfex+TeEeJVXb27bOHQD3+uP89sw0aeBuTiIiEtbg4GD/e7hTM7MorYc0a72MSbyixyooxcNttMH++/z4nnQSdOnkXk4iIFAjFitnN4pnt3g2vv+59POINJVb+pKZChw4wdqz/PgkJ9vjyokW9i0tERAqMnj2hfHnf9tRU72MRbyixcrNnDzRvDtOn++/z4ov2X8b553sXl4iIFChxcfD0077ts2Z5Hop4RFUs3Tz+OPz9t/u9s8+2U4OO421MIiJSIFWq5NuWmgoXXwwdO8Jll0HFit7HJaGhEavM1q6Fl15yv3f11fD990qqREQkYGXLurdPnw633w4nn2yTrL/+8jQsCRElVpnNn28Pd8psyBCYMAHi472PSURECqzGjaFataz7TJ8O1avDoUOehCQhpMQqM7e3DNWqwf33a6RKRERyLC7O1q5q1Cj7vgkJ9n18Wlro45LQUGKVmduKwjvv9DwMERGJHLVqwYIFMHhw9uupHngArr/effJEwp8Sq/Q+/xy+/NK3vUYN72MREZGIEh8PffrYhevz59uRLH8mTIC777blFKVgUWJ1zF9/uZ8/AFCnjqehiIhI5IqJgXPOgR074NJL/fcbORK6dIFdu7yLTfJOiVVaGowaZVcNumnbFurX9zYmERGJeImJ8NlndrN5hw7ufT74AM47z1Zrl4IhuhOrAwfgoouyXkP10UdatC4iIiHhONCiBUyd6l5IFODXX+3UoBQM0VMg9OhRSEmxU347dsDSpTBiRNav+egjKFnSk/BERCS69e1r11T973++9375xft4JHeiI7HasQPatYOffgqsf5Ei8MorOlxZREQ89fjjsGiRfV+f3mef2TIMRYrkT1wSuOiYCnz99cCTqrZtYeFCuPHG0MYkIiLiYuRI3xUo69fDDTeovlVBEB2J1YIFgfXr1w9mzIB69UIbj4iIiB8VKsDll/u2f/SRPfpmyxbvY5LARUditWlT1vfr17ffsf37exKOiIhIVp59FgoV8m3/6it7tuBVV8HKld7HJdlzTD5VH0tOTjYpKSnefDK3XX033wy1a9tdgWee6U0cIiIiARozJutVKSVK2OSqXDnvYopmjuMsNMYkZ9cvckesjLEFQM45x/3+88/Dww8rqRIRkbDUrZv/EgwAO3dmv7ldvBe5iVWPHnD11e7rq0qWhFKlvI9JREQkBx59FB55xP/9F1+Ev//2Lh7JXmQmVkuWwLvv+r/fpYs9U0BERCSMxcTAc8/BihVw5ZW+9/fuhbvu8j4u8S8ys4v58/3fO+MMuypQRESkgKhTBz78ELp29b03eTIMHux9TOIuMhOrFSt82+rWhdGjbfX10qW9j0lERCSPBg92n3B5+GFYvNj7eMRXZCZWy5f7tv3vf3YloNv+VRERkQKgUiXo08e3/ehReOYZ7+MRX5GZWPkbsRIRESngnn4aOnf2bZ80CbZt8z4eySjyEqv9++1By5nVqeN5KCIiIsFWqBC88477lGDZsvbomw0bvI9LrMhLrP74w9awSq9KFUhMzJ94REREgiwxERo1cr83dqwt4ajK7Pkj8hIrt/VVmgYUEZEIc8UV/u/9/Tecdx4cOOBdPGLF5XcAQdepEyxbZhOsFSvs1bBhfkclIiISVPfcY8s2Tpjgfn/jRnu4yA8/aDO8l6LjrEAREZEItWwZvP02DBnifr9uXZgzB8qU8TauSKOzAkVERKJA/fq2vtULL7jfX7HC7hgUbyixEhERiQC9e8NZZ7nfGznSd1+XhEZAiZXjOO0dx/ndcZxVjuP4PQ7ScZyrHMcxjuNkO1QmIiIiwVOkCHz3HdSr53tv6VK47DJbkUhCK9vEynGcWGAkcBFQH7jGcZz6Lv2SgLuBBcEOUkRERLJXpAjMnOl+b+pUaN8e1q3zNqZoE8iIVVNglTFmtTHmEDAB6OjS7yngBUCbO0VERPJJhQrwwAPu92bPhpo14amnNDUYKoEkVpWAf9J9nPpf23GO4zQCKhtjpmb1IMdxbnUcJ8VxnJQtW7bkOFgRERHJ3nPPwW23ud87eBCeeALefNPbmKJFIImV49J2PM91HCcGeBFwORYy04uMed0Yk2yMSS5XrlzgUYqIiEjA4uLg1Vdh1Cj/faZM8S6eaBJIYpUKVE738SnA+nQfJwGnA7Mcx/kLOAeYrAXsIiIi+euOO+D1193vTZmi6cBQCCSx+gmo7ThOdcdxCgFdgcnHbhpjdhpjyhpjqhljqgE/AJcZY1T9U0REJJ/dcgtMn+5+78knvY0lGmSbWBljjgC9gS+B5cAkY8xSx3GedBznslAHKCIiInlz9tkQ4/Ibf+hQjVoFW0B1rIwx04wxpxpjahpjnvmv7QljzGSXvq01WiUiIhI+SpWC/v1923ft8j+aJbmjyusiIiJR4PHHoXhx3/Zu3exhzhIcSqxERESigOPAiy/6tm/bBo0bZ72DUAKnxEpERCRKdO8OzZv7th8+DHfeCQMG2L9L7imxEhERiRIxMbb8QrFi7vf794fTToMff/Q0rIiixEpERCSK1K8P33wDtWq531+5Ei64AHbu9DauSKHESkREJMqcfTYsXgxXX+1+f9cuVWbPLSVWIiIiUahIERgzxv+ZgvPnextPpFBiJSIiEqUSEuyZgn37+t6bM6k/46YAABV2SURBVMf7eCKBEisREZEod+edvm2LF8M773gfS0GnxEpERCTKVawIdev6tvfqBcuWeR9PQabESkRERFynAw8cgOee8z6WgkyJlYiIiHDddXD99b7tX3wBBw96H09BpcRKREREcBx46SXf9q1b4aOPvI+noFJiJSIiIgCULAlXXunbrh2CgVNiJSIiIsd16uTb9tVXYIz3sRRESqxERETkuPPOs9OC6f35J8yenT/xFDRKrEREROS4U06B//s/3/ZrrrFH3UjWlFiJiIhIBt27+7Zt2GDXXx096nk4BYoSKxEREcng0kshKcm3/Ztv4JNPvI+nIFFiJSIiIhkULw4vv+x+b+JEb2MpaJRYiYiIiI9u3eDJJ33bV6zwPpaCRImViIiIuOrZ07ft77+9j6MgUWIlIiIiripUgPj4jG07d9pL3CmxEhEREVcxMbb8QmbLlnkfS0GhxEpERET8qlrVt230aO/jKCiUWImIiIhfbsVCP/hA9az8UWIlIiIift15JxQtmrFt+3Z7zI34UmIlIiIifpUqBaef7tuuxMqdEisRERHJUq1avm3TpnkfR0GgxEpERESydNFFvm1jx2qdlRslViIiIpKliy+G2NiMbf/+a9dfSUZKrERERCRLpUtDq1a+7a++CosWeR9POFNiJSIiItl6+GH3diVWGSmxEhERkWy1a2ePuMns4EHvYwlnSqxEREQkIF27+ratW+d9HOFMiZWIiIgEpG5d37ZZszwPI6wpsRIREZGAtG3r2/b99/Dzz97HEq6UWImIiEhAateGSpV82y+8ELZt8z6ecKTESkRERALiOHDppb7tW7fC8897H084UmIlIiIiARswwJ4fmNnUqd7HEo6UWImIiEjAypeH6dN925cvhz17vI8n3CixEhERkRw5+2yoXNm3/b33vI8l3CixEhERkRxzW8Teqxc89BAY43084UKJlYiIiOTYFVe4tw8aBG+95W0s4USJlYiIiOTYXXdBvXru96J5IbsSKxEREcmxIkXgyy/d7/3wA6SleRtPuFBiJSIiIrlSuTJs3OjbvmkTzJ/vfTzhQImViIiI5FqFCnDBBb7tzz4bnYvYlViJiIhInrglVtOnw7vveh5KvlNiJSIiInnSoweUK+fbfs89sHat9/HkJyVWIiIikidlysBrr/m2794Nzz3nfTz5SYmViIiI5NkVV0C3br7tn34KR496H09+UWIlIiIiQTFkCMTHZ2zbtAnmzs2fePKDEisREREJirJl3Rey9+sXPTsElViJiIhI0HTt6ts2cyZ88IH3seQHJVYiIiISNF27Qs2avu3DhnkfS35QYiUiIiJBEx8PI0b4ts+fD7/+6n08XlNiJSIiIkF10UXQoIFv+yefeB+L15RYiYiISFA5DnTv7tu+apXnoXhOiZWIiIgEXfXqvm07d3ofh9eUWImIiEjQJST4tv3zj/dxeE2JlYiIiARdUpJv26+/wmefeR+Ll5RYiYiISNDVr+/efs01sGaNt7F4SYmViIiIBF2pUnZ3YGb798O993ofj1eUWImIiEhIjB4NTZr4tk+eHLmlF5RYiYiISEiUKwdffQUVK/reu+02OHjQ+5hCTYmViIiIhEzp0vDii77tW7bAjBnexxNqASVWjuO0dxznd8dxVjmO84jL/fsdx1nmOM5ix3FmOI5TNfihioiISEF09dVQ1SUzmDnT+1hCLdvEynGcWGAkcBFQH7jGcZzMa/1/AZKNMWcCHwIvBDtQERERKZgcB3r29G0fPBiM8T6eUApkxKopsMoYs9oYcwiYAHRM38EYM9MYs++/D38ATglumCIiIlKQdezo3h5pOwQDSawqAelrpab+1+bPzcB0txuO49zqOE6K4zgpW7ZsCTxKERERKdDq1YPERN/2ESPg55+9jydUAkmsHJc214E7x3GuB5KBQW73jTGvG2OSjTHJ5cqVCzxKERERKdDi4tynAwGmTPE2llAKJLFKBSqn+/gUYH3mTo7jnA/0BS4zxkTgBkoRERHJi0GD4KyzfNu//tr7WEIlkMTqJ6C24zjVHccpBHQFJqfv4DhOI+A1bFK1OfhhioiISEEXHw/vv+/bnpICaWnexxMK2SZWxpgjQG/gS2A5MMkYs9RxnCcdx7nsv26DgGLAB47jLHIcZ7Kfx4mIiEgUq1ULSpbM2HbwIPz9d/7EE2xxgXQyxkwDpmVqeyLd388PclwiIiISgRwH6tSBBQsytq9eDdWr509MwaTK6yIiIuKp0qV923bs8D6OUFBiJSIiIp6KjfVtGzfO+zhCQYmViIiIeOrMM33bpk6NjFErJVYiIiLiqW7d7A7B9A4dgs8/z594gkmJlYiIiHiqTh246Sbf9kiYDlRiJSIiIp7r0sW3bdo0mD/f+1iCSYmViIiIeO6886BqVd/2Bx/0PpZgUmIlIiIinouLg0ce8W2fOxdWrPA+nmBRYiUiIiL54pZb3IuC9uvnfSzBosRKRERE8kVsLNxwg2/7lCl2l2BBpMRKRERE8s199/m27d8Pu3Z5H0swKLESERGRfFOyJNSo4du+fr33sQSDEisRERHJV9Wq+bYtXOh5GEGhxEpERETylVtiddttsHOn56HkmRIrERERyVennurbdvgwDBnifSx5pcRKRERE8lWXLuA4vu0//X97dx9kV33Xcfz9BRpIeRyaBjI8RYYQi4BBIhA6CBQbAn+AtEAJUkB50PERraKdqsW240DxYWREkCKlMEDBlEJEoVVJAgMkkhbkSRmYlicb2NJAalielnz945xtNnvv3T27uXvuueT9mtm5ub/7u+d+7/6ym0/O73d/5+H6a9lcBitJktRTs2fD5Ze3tq9ZU3spm81gJUmSeu6001rbDFaSJEmTsPvurW0DA7B2bf21bA6DlSRJ6rlp02Du3Nb2Zcvqr2VzGKwkSVIjHHdca9uSJfXXsTkMVpIkqREWLmxtW7oUhobqr2WyDFaSJKkRTjihmBIcaXAQnniiN/VMhsFKkiQ1wrRpcNBBre0331x/LZNlsJIkSY1x+umtbStW1F/HZBmsJElSY5xxRmvbI4/AW2/VX8tkGKwkSVJj7LUXzJq1adu778Lq1b2pZ6IMVpIkqTEiYMGC1vYbbqi/lskwWEmSpEY5/vjWtmuvhQ0b6q9logxWkiSpUc48E6ZP37QtE846C958szc1VWWwkiRJjbLDDrD//q3tt9xSnM167736a6rKYCVJkhpn3rz27fffDytX1lvLRBisJElS41x8ceunA4d9+9v11jIRBitJktQ4BxxQ7F/Vzu2311vLRBisJElSI+22G6xa1dr+xBPw4ov111OFwUqSJDXWYYe1X2/1zDP111KFwUqSJDXazJmtbe+8U38dVRisJElSo02b1trW1P2sDFaSJKnRttuuta3d2qsmMFhJkqRGa7fG6qtfhbffrr+W8RisJElSo51zDmyzzaZtAwNw7729qWcsBitJktRoe+4Jn/hEa/sDD9Rfy3gMVpIkqfEWLmxte/DB+usYj8FKkiQ13pFHtratWgVDQ/XXMhaDlSRJary5c2HXXTdtGxyExx7rTT2dGKwkSVLjbbUVLFjQ2t60dVYGK0mS1BfaBaunnqq/jrEYrCRJUl+YM6e1bc2a+usYi8FKkiT1hVmzWttefrn+OsZisJIkSX1hp51a2wYH669jLAYrSZLUF9pdM3D9+vrrGIvBSpIk9YXddmtte/75Zp21MlhJkqS+sMsusM8+m7Zt2ACPP96betoxWEmSpL4xb15r26OP1l9HJwYrSZLUN9oFq4cfrr+OTgxWkiSpb7QLVkuWwBtv1F9LOwYrSZLUN449FqZP37Rt3Tq47bbe1DOawUqSJPWNnXeGxYtb2++7r/5a2jFYSZKkvrJoUWtbUy5tY7CSJEl9Za+9WtsGBuqvox2DlSRJ6ivtNgp95ZX662jHYCVJkvrKzJmtbQMDkFl/LaMZrCRJUl/Zfvvia6ShIXjttd7UM5LBSpIk9Z1OZ616zWAlSZL6TlPXWRmsJElS32kXrJpwxmqbXhcgSZI0UfvuC3PmFAFr5szidp99el0VRFZYQh8Ri4C/BbYGrs3MS0c9vi1wA3Ao8CPgU5n53FjHnD9/fq5evXqSZUuSJNUnIr6TmfPH6zfuVGBEbA1cCZwAHAAsjogDRnU7D3gtM/cD/ga4bOIlS5Ik9bcqa6wOA57NzO9l5jvA14GTR/U5Gfha+eclwHEREd0rU5IkqfmqrLHaA3hxxP2XgMM79cnMoYhYB3wIeHVkp4i4ELiwvLs+Ip4edZwZo5+jxnGM+oPj1B8cp+ZzjPpDHeNUaQVXlWDV7szT6IVZVfqQmdcA13R8oYjVVeYv1TuOUX9wnPqD49R8jlF/aNI4VZkKfAkYebnDPYEfdOoTEdsAOwNru1GgJElSv6gSrB4G5kTET0XENOAMYOmoPkuBc8o/nwrcm1U+bihJkvQ+Mu5UYLlm6reAb1Fst3BdZj4ZEV8AVmfmUuAfgRsj4lmKM1VnTLKejtOEagzHqD84Tv3BcWo+x6g/NGacKu1jJUmSpPF5SRtJkqQuMVhJkiR1SU+CVUQsioinI+LZiPjjNo9vGxG3lo+viojZ9Ve5ZaswRr8fEU9FxGMR8R8R0YArNG15xhunEf1OjYiMiEZ8HHlLUmWMIuL08ufpyYi4ue4aVel33t4RsSwiHil/753Yizq3ZBFxXUQMRMQTHR6PiLiiHMPHIuLn6q4RehCsvERO81Uco0eA+Zl5MMVu+1+ut0pVHCciYkfgd4BV9VaoKmMUEXOAzwIfzcyfAS6qvdAtXMWfpT8BbsvMQyg+oPX39VYp4Hpg0RiPnwDMKb8uBK6qoaYWvThj5SVymm/cMcrMZZk5WN5dSbG/mepV5WcJ4IsUwfetOosTUG2MLgCuzMzXADJzoOYaVW2cEtip/PPOtO7nqCmWmfcx9h6ZJwM3ZGElsEtEzKqnuo16EazaXSJnj059MnMIGL5EjupRZYxGOg+4e0orUjvjjlNEHALslZl31VmYfqLKz9L+wP4R8UBErIyIsf5HrqlRZZwuAc6KiJeAfwV+u57SNAET/bdrSlS5pE23de0SOZoylb//EXEWMB84ekorUjtjjlNEbEUxlX5uXQWpRZWfpW0opi6OoTjze39EHJiZr09xbdqoyjgtBq7PzL+KiAUUezcemJkbpr48VdSI7NCLM1ZeIqf5qowREfGLwOeAkzLz7Zpq00bjjdOOwIHA8oh4DjgCWOoC9lpV/X13Z2a+m5nfB56mCFqqT5VxOg+4DSAzHwK2o7jwr5qj0r9dU60XwcpL5DTfuGNUTjH9A0Wock1Ib4w5Tpm5LjNnZObszJxNsRbupMxc3Ztyt0hVft/dARwLEBEzKKYGv1drlaoyTi8AxwFExEcogtUPa61S41kKnF1+OvAIYF1mrqm7iNqnAmu+RI4moeIYXQ7sAPxT+bmCFzLzpJ4VvQWqOE7qoYpj9C1gYUQ8BbwH/GFm/qh3VW95Ko7TZ4CvRMTvUUwvnet/+OsVEbdQTJnPKNe6fR74AEBmXk2x9u1E4FlgEPiVntTp3wtJkqTucOd1SZKkLjFYSZIkdYnBSpIkqUsMVpIkSV1isJIkSeoSg5WkzRIRyyOi0R8vjohjIiIj4pIpOv4l5fGPmYr+kvqHwUrSuKY6mEjS+4XBSpIkqUsMVpIkSV1isJI0pnL6b1l59/PllGCOXlcVEdMi4ksR8UJEvB0RT0bE4jbHu758/n4R8bmIeDYi3o2Ii0b02SMiroqI58tj/SAiromI3dscb2FE/HtEvBIRb5V974mIj3d4P0dExLKIWB8RayPipoj4cJt+ERG/HhHfjYjBiFgXEfdGxPET+N7tGBFXRMTL5TEeiojjqj5fUv+p/VqBkvrOcmA2xYXRV5T32/k6cAhwF8XvlsXAzRHxembe3ab/lcC8sv/rFFemJyLmlq8zo3zsGWA/4Hzg4xHx85n5atn3JOBOYA3wTeA1YBZwJHA88G+jXvMw4I/K9quBjwJnAvtGxJGjrv12NXAhxQWRrwKmA58C7o6I8zPzug7fB8ratgb+BTgKWEURTveluJ7ZirGeK6l/GawkjSkzl5cX2j4HWJ6Zl3ToujtwUGauB4iImyhC2EVAu2C1PzCvzdXnvwbsAhyVmQ8NN0bEKcDtwBeA3yibzwXeKY8zMPIgEbFrm9c8ATg1M79R9tmKImR9DDgCeKhs/xhFqFoNHJ2Zg2X7pcB3gb+LiH/OzB92+F4A/CpFqLoVWDwc2iLi7PI9SnofcipQUrd8djhUAWTmCuA5YH6H/n85OlRFxKHA4cDVI0NVebxvUgSd00cd512KcMWo/mvbvOby4VBV9tkA3FDeHVnnp8vbPxsOVWX/F4ArKM5endbhfQ37ZSCBPx11JuxG4H/Gea6kPuUZK0nd8kibtv8F9u7Qf3WbtsPK2707bO3wQeBDETGjnA68FTgFeDwibqaYbnsgM/9vgjVCcZZs2M+Wt/e16b9iVJ9ODgYGMvOZkY2ZmRHxIPDT4zxfUh8yWEnqisz8cZvmITqfGR9o0zY8fXdK+dXJ9sCrmXlrRAwBnwH+ALgYeCci7gAuajPN2KlGgK1HtO0ErM/MN9r0f2VEn7HsBPx3h8favXdJ7wNOBUrqlXa7tQ8Hn3MzM8b4ev4nB8n8RmYeCXyYIozdRTFdeOtm1PZjYIeI+GCbx3YbVetYx2j5tGFp5mQLk9RsBitJVbxX3m49Zq/N95/l7RETfWJmrs3MOzLzkxSfwjsqInYZ73kd/Fd5+wttHjuqvH10nGM8BsyMiDkjG6P4JMCCSdYlqeEMVpKqGF4IvsdUvkhmrqJYe3V+u/2iImJ6RBw+4v7HImLbUX22pVgvNcTGab6JurG8/fOI2G7EsfcEfhd4E1gyzjFuAgL4Yhmmhn0a+Mgk65LUcK6xklTF0xR7RS2OiLcpF3xn5pem4LXOpFiEfk9ELKM4e7QVxV5aRwMrgUVl378G9oiIFcD3gQ9Q7F81l+KTheuZhMy8NyK+AlxAsTD+TjbuY7UrcME4Wy0AXAecXT5ndvle9gV+iWKLh7YbmErqbwYrSePKzKGIOBW4jOKMy/blQ10PVpn5TEQcQrEQ/SSKzT7fothA9EY2bo8AcCnwSeBQ4ERgkGJD0fOA6zezlF+j+BThhcBvUmzr8B3gssy8p8L7eC8iTgT+giJcHUwREk+kmE40WEnvQ7Hp9iqSJEmaLNdYSZIkdYnBSpIkqUsMVpIkSV1isJIkSeoSg5UkSVKXGKwkSZK6xGAlSZLUJQYrSZKkLjFYSZIkdcn/AwQQzGsj8n0zAAAAAElFTkSuQmCC\n",
      "text/plain": [
       "<matplotlib.figure.Figure at 0x20c86d13c88>"
      ]
     },
     "metadata": {},
     "output_type": "display_data"
    }
   ],
   "source": [
    "def plot_precision_and_recall(precision, recall, threshold):\n",
    "    plt.plot(threshold, precision[:-1], \"r-\", label=\"precision\", linewidth=5)\n",
    "    plt.plot(threshold, recall[:-1], \"b\", label=\"recall\", linewidth=5)\n",
    "    plt.xlabel(\"threshold\", fontsize=19)\n",
    "    plt.legend(loc=\"upper right\", fontsize=19)\n",
    "    plt.ylim([0, 1])\n",
    "\n",
    "plt.figure(figsize=(10, 7))\n",
    "plot_precision_and_recall(precision, recall, threshold)\n",
    "plt.show()"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "Above you can clearly see that the recall is falling of rapidly at a precision of around 85%.  Because of that you may want to select the precision/recall tradeoff before that - maybe at around 75 %.\n",
    "\n",
    "You are now able to choose a threshold, that gives you the best precision/recall tradeoff for your current machine learning problem. If you want for example a precision of 80%, you can easily look at the plots and see that you would need a threshold of around 0.4.  Then you could train a model with exactly that threshold and would get the desired accuracy.\n",
    "\n",
    "\n",
    "Another way is to plot the precision and recall against each other:"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 316,
   "metadata": {},
   "outputs": [
    {
     "data": {
      "image/png": "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\n",
      "text/plain": [
       "<matplotlib.figure.Figure at 0x20c812bdb38>"
      ]
     },
     "metadata": {},
     "output_type": "display_data"
    }
   ],
   "source": [
    "def plot_precision_vs_recall(precision, recall):\n",
    "    plt.plot(recall, precision, \"g--\", linewidth=2.5)\n",
    "    plt.ylabel(\"recall\", fontsize=19)\n",
    "    plt.xlabel(\"precision\", fontsize=19)\n",
    "    plt.axis([0, 1.5, 0, 1.5])\n",
    "\n",
    "plt.figure(figsize=(14, 7))\n",
    "plot_precision_vs_recall(precision, recall)\n",
    "plt.show()"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "## ROC AUC Curve\n",
    "\n",
    "Another way to evaluate and compare your binary classifier is provided by the ROC AUC Curve. This curve plots the true positive rate (also called recall) against the false positive rate (ratio of incorrectly classified negative instances), instead of plotting the precision versus the recall."
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 318,
   "metadata": {},
   "outputs": [],
   "source": [
    "from sklearn.metrics import roc_curve\n",
    "# compute true positive rate and false positive rate\n",
    "false_positive_rate, true_positive_rate, thresholds = roc_curve(Y_train, y_scores)"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 320,
   "metadata": {},
   "outputs": [
    {
     "data": {
      "image/png": "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\n",
      "text/plain": [
       "<matplotlib.figure.Figure at 0x20c851f4400>"
      ]
     },
     "metadata": {},
     "output_type": "display_data"
    }
   ],
   "source": [
    "# plotting them against each other\n",
    "def plot_roc_curve(false_positive_rate, true_positive_rate, label=None):\n",
    "    plt.plot(false_positive_rate, true_positive_rate, linewidth=2, label=label)\n",
    "    plt.plot([0, 1], [0, 1], 'r', linewidth=4)\n",
    "    plt.axis([0, 1, 0, 1])\n",
    "    plt.xlabel('False Positive Rate (FPR)', fontsize=16)\n",
    "    plt.ylabel('True Positive Rate (TPR)', fontsize=16)\n",
    "\n",
    "plt.figure(figsize=(14, 7))\n",
    "plot_roc_curve(false_positive_rate, true_positive_rate)\n",
    "plt.show()"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "The red line in the middel represents a purely random classifier (e.g a coin flip) and therefore your classifier should be as far away from it as possible. Our Random Forest model seems to do a good job. \n",
    "\n",
    "Of course we also have a tradeoff here, because the classifier produces more false positives, the higher the true positive rate is. "
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 322,
   "metadata": {},
   "outputs": [
    {
     "name": "stdout",
     "output_type": "stream",
     "text": [
      "ROC-AUC-Score: 0.9446601476368517\n"
     ]
    }
   ],
   "source": [
    "from sklearn.metrics import roc_auc_score\n",
    "r_a_score = roc_auc_score(Y_train, y_scores)\n",
    "print(\"ROC-AUC-Score:\", r_a_score)"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 323,
   "metadata": {},
   "outputs": [],
   "source": [
    "submission = pd.DataFrame({\n",
    "        \"PassengerId\": test_df[\"PassengerId\"],\n",
    "        \"Survived\": Y_prediction\n",
    "    })\n",
    "submission.to_csv('submission.csv', index=False)"
   ]
  }
 ],
 "metadata": {
  "kernelspec": {
   "display_name": "Python 3",
   "language": "python",
   "name": "python3"
  },
  "language_info": {
   "codemirror_mode": {
    "name": "ipython",
    "version": 3
   },
   "file_extension": ".py",
   "mimetype": "text/x-python",
   "name": "python",
   "nbconvert_exporter": "python",
   "pygments_lexer": "ipython3",
   "version": "3.6.4"
  }
 },
 "nbformat": 4,
 "nbformat_minor": 2
}
