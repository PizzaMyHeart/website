---
title: "Canine S-index"
description: 
date: 2021-02-03
tags: ["projects", "data"]
layout: layouts/post.njk
---


*Or, which of these doggos is most similar to a sausage?*

[View on GitHub](https://github.com/PizzaMyHeart/doggo-dimensions/blob/main/sausageness.ipynb)

Sausage dogs are among the cutest creatures in existence, no doubt. Just look at this majestic unit:

<img src='https://upload.wikimedia.org/wikipedia/commons/thumb/2/27/Short-haired-Dachshund.jpg/330px-Short-haired-Dachshund.jpg' alt='Dachshund' width='400'>

_Credit: Igor Bredikhin via Wikipedia_

Corgis and Basset Hounds are two other sausage dogs in contention for ultimate sausage-ness, but which of these sausage dogs are objectively closer to the sausage ideal? Where do other dog breeds fall on the sausage-ness spectrum? These are important questions that need answering. 

The most important metric to consider when measuring sausage-ness is, of course, the side profile of the dog: is it longer than it is tall? We can use the ratio of (back) length to height to compare dogs on this metric. I'm going to call this ratio the `S-index` in my code, but you can call it sausage-ness if you like.




```python
import matplotlib.pyplot as plt
import pandas as pd
import seaborn as sns
```

## Data source

It's surprisingly difficult to find morphometric data for dog breeds. The [American Kennel Club](https://akc.org) provides height and weight information but not back length, which is crucial in determining S-index. The authors of [this paper](https://www.ncbi.nlm.nih.gov/pmc/articles/PMC2748280/) collected a large variety of measurements, including back length, but they haven't supplied any raw data. If this were a proper scientific study, I'd have to contact the authors with a request for data, but I've already invested more time than I should in this study of sausage dogs, so here we go.

The design-focused website [dimensions.com](https://www.dimensions.com/collection/dogs-dog-breeds) does provide back lengths. Instead of the [withers height](https://en.wikipedia.org/wiki/Withers) which is used by the American Kennel Club, height in the rest of this notebook refers to standing height. This is simply because I don't currently have the time to collect the withers height. Using withers height would probably give us a better indicator of sausage-ness.

Note that this website doesn't cite the source of their data, so take this otherwise scientifically rigorous study of sausage dogs with a large heap of salt. Only 51 dog breeds are available here, which is an okay but rather modest sample size. Furthermore, only minimum and maximum values are provided, so I had to settle with using [mid-range](https://en.wikipedia.org/wiki/Mid-range) as a measure of central tendency.

I collected the necessary data from [dimensions.com](https://www.dimensions.com/collection/dogs-dog-breeds) and stored them in a csv file. All measurements are in metric units. No cleaning necessary, which is always nice!


```python
df = pd.read_csv('data.csv')
df 
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>breed</th>
      <th>length_min</th>
      <th>length_max</th>
      <th>height_min</th>
      <th>height_max</th>
      <th>weight_min</th>
      <th>weight_max</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Pomeranian</td>
      <td>24</td>
      <td>28</td>
      <td>20</td>
      <td>24</td>
      <td>1.4</td>
      <td>3.2</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Chihuahua</td>
      <td>24</td>
      <td>38</td>
      <td>22</td>
      <td>33</td>
      <td>1.4</td>
      <td>2.7</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Yorkshire Terrier</td>
      <td>30</td>
      <td>39</td>
      <td>27</td>
      <td>33</td>
      <td>1.8</td>
      <td>5.4</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Papillon</td>
      <td>30</td>
      <td>43</td>
      <td>30</td>
      <td>42</td>
      <td>2.3</td>
      <td>4.5</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Maltese</td>
      <td>34</td>
      <td>44</td>
      <td>30</td>
      <td>38</td>
      <td>1.4</td>
      <td>3.6</td>
    </tr>
    <tr>
      <th>5</th>
      <td>Shih Tzu</td>
      <td>38</td>
      <td>44</td>
      <td>33</td>
      <td>38</td>
      <td>4.1</td>
      <td>7.3</td>
    </tr>
    <tr>
      <th>6</th>
      <td>Cavalier King Charles Spaniel</td>
      <td>48</td>
      <td>51</td>
      <td>38</td>
      <td>42</td>
      <td>5.9</td>
      <td>8.2</td>
    </tr>
    <tr>
      <th>7</th>
      <td>Pug</td>
      <td>39</td>
      <td>52</td>
      <td>33</td>
      <td>43</td>
      <td>6.4</td>
      <td>8.2</td>
    </tr>
    <tr>
      <th>8</th>
      <td>Jack Russell Terrier</td>
      <td>46</td>
      <td>55</td>
      <td>36</td>
      <td>43</td>
      <td>4.1</td>
      <td>6.8</td>
    </tr>
    <tr>
      <th>9</th>
      <td>French Bulldog</td>
      <td>46</td>
      <td>55</td>
      <td>39</td>
      <td>47</td>
      <td>7.3</td>
      <td>12.7</td>
    </tr>
    <tr>
      <th>10</th>
      <td>Bichon Frise</td>
      <td>46</td>
      <td>56</td>
      <td>33</td>
      <td>41</td>
      <td>5.4</td>
      <td>8.2</td>
    </tr>
    <tr>
      <th>11</th>
      <td>Beagle</td>
      <td>51</td>
      <td>64</td>
      <td>46</td>
      <td>56</td>
      <td>9.0</td>
      <td>11.0</td>
    </tr>
    <tr>
      <th>12</th>
      <td>Dachshund</td>
      <td>55</td>
      <td>64</td>
      <td>33</td>
      <td>37</td>
      <td>7.3</td>
      <td>14.5</td>
    </tr>
    <tr>
      <th>13</th>
      <td>Basenji</td>
      <td>58</td>
      <td>64</td>
      <td>48</td>
      <td>51</td>
      <td>9.1</td>
      <td>11.8</td>
    </tr>
    <tr>
      <th>14</th>
      <td>Havanese</td>
      <td>48</td>
      <td>65</td>
      <td>32</td>
      <td>43</td>
      <td>3.2</td>
      <td>5.9</td>
    </tr>
    <tr>
      <th>15</th>
      <td>Pembroke Welsh Corgi</td>
      <td>56</td>
      <td>66</td>
      <td>36</td>
      <td>43</td>
      <td>10.0</td>
      <td>14.0</td>
    </tr>
    <tr>
      <th>16</th>
      <td>Bulldog</td>
      <td>51</td>
      <td>69</td>
      <td>38</td>
      <td>48</td>
      <td>18.0</td>
      <td>25.0</td>
    </tr>
    <tr>
      <th>17</th>
      <td>Shiba Inu</td>
      <td>58</td>
      <td>71</td>
      <td>44</td>
      <td>55</td>
      <td>6.8</td>
      <td>10.9</td>
    </tr>
    <tr>
      <th>18</th>
      <td>Cocker Spaniel</td>
      <td>61</td>
      <td>74</td>
      <td>46</td>
      <td>56</td>
      <td>12.0</td>
      <td>16.0</td>
    </tr>
    <tr>
      <th>19</th>
      <td>Whippet</td>
      <td>58</td>
      <td>79</td>
      <td>61</td>
      <td>71</td>
      <td>7.0</td>
      <td>14.0</td>
    </tr>
    <tr>
      <th>20</th>
      <td>Standard Schnauzer</td>
      <td>71</td>
      <td>79</td>
      <td>58</td>
      <td>66</td>
      <td>14.0</td>
      <td>20.0</td>
    </tr>
    <tr>
      <th>21</th>
      <td>Standard Poodle</td>
      <td>62</td>
      <td>81</td>
      <td>61</td>
      <td>83</td>
      <td>20.0</td>
      <td>32.0</td>
    </tr>
    <tr>
      <th>22</th>
      <td>English Springer Spaniel</td>
      <td>69</td>
      <td>84</td>
      <td>65</td>
      <td>79</td>
      <td>18.0</td>
      <td>25.0</td>
    </tr>
    <tr>
      <th>23</th>
      <td>Australian Cattle Dog</td>
      <td>71</td>
      <td>84</td>
      <td>53</td>
      <td>64</td>
      <td>14.0</td>
      <td>16.0</td>
    </tr>
    <tr>
      <th>24</th>
      <td>Dalmatian</td>
      <td>79</td>
      <td>84</td>
      <td>70</td>
      <td>76</td>
      <td>20.0</td>
      <td>32.0</td>
    </tr>
    <tr>
      <th>25</th>
      <td>Border Collie</td>
      <td>71</td>
      <td>86</td>
      <td>56</td>
      <td>69</td>
      <td>12.0</td>
      <td>20.0</td>
    </tr>
    <tr>
      <th>26</th>
      <td>Collie</td>
      <td>74</td>
      <td>86</td>
      <td>66</td>
      <td>81</td>
      <td>18.0</td>
      <td>29.0</td>
    </tr>
    <tr>
      <th>27</th>
      <td>Siberian Husky</td>
      <td>76</td>
      <td>88</td>
      <td>67</td>
      <td>79</td>
      <td>15.9</td>
      <td>29.5</td>
    </tr>
    <tr>
      <th>28</th>
      <td>Basset Hound</td>
      <td>66</td>
      <td>89</td>
      <td>41</td>
      <td>53</td>
      <td>20.0</td>
      <td>29.0</td>
    </tr>
    <tr>
      <th>29</th>
      <td>Boxer</td>
      <td>76</td>
      <td>89</td>
      <td>72</td>
      <td>84</td>
      <td>22.7</td>
      <td>36.3</td>
    </tr>
    <tr>
      <th>30</th>
      <td>Samoyed</td>
      <td>72</td>
      <td>90</td>
      <td>62</td>
      <td>76</td>
      <td>15.9</td>
      <td>29.5</td>
    </tr>
    <tr>
      <th>31</th>
      <td>Australian Shepherd</td>
      <td>71</td>
      <td>91</td>
      <td>64</td>
      <td>81</td>
      <td>16.0</td>
      <td>32.0</td>
    </tr>
    <tr>
      <th>32</th>
      <td>Bull Terrier</td>
      <td>80</td>
      <td>98</td>
      <td>60</td>
      <td>74</td>
      <td>20.4</td>
      <td>36.3</td>
    </tr>
    <tr>
      <th>33</th>
      <td>Belgian Malinois</td>
      <td>86</td>
      <td>102</td>
      <td>72</td>
      <td>84</td>
      <td>18.1</td>
      <td>36.3</td>
    </tr>
    <tr>
      <th>34</th>
      <td>German Pointer</td>
      <td>86</td>
      <td>104</td>
      <td>53</td>
      <td>64</td>
      <td>20.0</td>
      <td>32.0</td>
    </tr>
    <tr>
      <th>35</th>
      <td>Alaskan Malamute</td>
      <td>89</td>
      <td>105</td>
      <td>75</td>
      <td>88</td>
      <td>34.0</td>
      <td>39.0</td>
    </tr>
    <tr>
      <th>36</th>
      <td>Bernese Mountain Dog</td>
      <td>89</td>
      <td>105</td>
      <td>76</td>
      <td>91</td>
      <td>32.0</td>
      <td>52.0</td>
    </tr>
    <tr>
      <th>37</th>
      <td>Newfoundland</td>
      <td>93</td>
      <td>107</td>
      <td>83</td>
      <td>95</td>
      <td>45.4</td>
      <td>68.0</td>
    </tr>
    <tr>
      <th>38</th>
      <td>Golden Retriever</td>
      <td>94</td>
      <td>107</td>
      <td>72</td>
      <td>80</td>
      <td>25.0</td>
      <td>34.0</td>
    </tr>
    <tr>
      <th>39</th>
      <td>Rottweiler</td>
      <td>98</td>
      <td>107</td>
      <td>77</td>
      <td>86</td>
      <td>42.0</td>
      <td>55.0</td>
    </tr>
    <tr>
      <th>40</th>
      <td>German Shepherd</td>
      <td>91</td>
      <td>108</td>
      <td>67</td>
      <td>79</td>
      <td>23.0</td>
      <td>41.0</td>
    </tr>
    <tr>
      <th>41</th>
      <td>Great Dane</td>
      <td>90</td>
      <td>109</td>
      <td>108</td>
      <td>126</td>
      <td>50.0</td>
      <td>79.0</td>
    </tr>
    <tr>
      <th>42</th>
      <td>Akita</td>
      <td>93</td>
      <td>110</td>
      <td>79</td>
      <td>93</td>
      <td>31.8</td>
      <td>59.0</td>
    </tr>
    <tr>
      <th>43</th>
      <td>Tibetan Mastiff</td>
      <td>90</td>
      <td>113</td>
      <td>72</td>
      <td>90</td>
      <td>31.8</td>
      <td>68.0</td>
    </tr>
    <tr>
      <th>44</th>
      <td>Bullmastiff</td>
      <td>102</td>
      <td>113</td>
      <td>76</td>
      <td>86</td>
      <td>45.4</td>
      <td>59.0</td>
    </tr>
    <tr>
      <th>45</th>
      <td>Doberman Pinscher</td>
      <td>102</td>
      <td>117</td>
      <td>76</td>
      <td>90</td>
      <td>27.2</td>
      <td>45.4</td>
    </tr>
    <tr>
      <th>46</th>
      <td>Saint Bernard</td>
      <td>102</td>
      <td>119</td>
      <td>81</td>
      <td>94</td>
      <td>54.0</td>
      <td>82.0</td>
    </tr>
    <tr>
      <th>47</th>
      <td>Irish Wolfhound</td>
      <td>117</td>
      <td>128</td>
      <td>104</td>
      <td>114</td>
      <td>52.2</td>
      <td>81.6</td>
    </tr>
    <tr>
      <th>48</th>
      <td>Great Pyrenees</td>
      <td>103</td>
      <td>133</td>
      <td>81</td>
      <td>103</td>
      <td>36.3</td>
      <td>54.4</td>
    </tr>
    <tr>
      <th>49</th>
      <td>Mastiff</td>
      <td>113</td>
      <td>135</td>
      <td>89</td>
      <td>107</td>
      <td>54.4</td>
      <td>104.3</td>
    </tr>
    <tr>
      <th>50</th>
      <td>Cane Corso</td>
      <td>105</td>
      <td>140</td>
      <td>83</td>
      <td>109</td>
      <td>38.6</td>
      <td>49.9</td>
    </tr>
  </tbody>
</table>
</div>




```python
def calc_mid(a, b):
    '''
    Get mid-range from min and max. 
    '''
    return (a+b) / 2
```


```python
df['length_mid'] = df.apply(lambda x: calc_mid(x['length_min'], x['length_max']), axis=1)
df['height_mid'] = df.apply(lambda x: calc_mid(x['height_min'], x['height_max']), axis=1)
df['s_index'] = df['length_mid'] / df['height_mid']
```


```python
df.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>breed</th>
      <th>length_min</th>
      <th>length_max</th>
      <th>height_min</th>
      <th>height_max</th>
      <th>weight_min</th>
      <th>weight_max</th>
      <th>length_mid</th>
      <th>height_mid</th>
      <th>s_index</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Pomeranian</td>
      <td>24</td>
      <td>28</td>
      <td>20</td>
      <td>24</td>
      <td>1.4</td>
      <td>3.2</td>
      <td>26.0</td>
      <td>22.0</td>
      <td>1.181818</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Chihuahua</td>
      <td>24</td>
      <td>38</td>
      <td>22</td>
      <td>33</td>
      <td>1.4</td>
      <td>2.7</td>
      <td>31.0</td>
      <td>27.5</td>
      <td>1.127273</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Yorkshire Terrier</td>
      <td>30</td>
      <td>39</td>
      <td>27</td>
      <td>33</td>
      <td>1.8</td>
      <td>5.4</td>
      <td>34.5</td>
      <td>30.0</td>
      <td>1.150000</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Papillon</td>
      <td>30</td>
      <td>43</td>
      <td>30</td>
      <td>42</td>
      <td>2.3</td>
      <td>4.5</td>
      <td>36.5</td>
      <td>36.0</td>
      <td>1.013889</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Maltese</td>
      <td>34</td>
      <td>44</td>
      <td>30</td>
      <td>38</td>
      <td>1.4</td>
      <td>3.6</td>
      <td>39.0</td>
      <td>34.0</td>
      <td>1.147059</td>
    </tr>
  </tbody>
</table>
</div>




```python
sns.set_theme()
sns.set(font='monospace')

```

## Plots

### Scatter plot

I initially planned to display the data in a scatter plot, but labelling each point would create too much visual clutter. Nonetheless, it shows that most dog breeds in our sample are longer than they are tall.


```python
g = sns.lmplot(data=df, x='height_mid', y='length_mid')
```


    
![Scatter plot](/img/sausageness-scatter.png)
    


### Bar plot

We lose information about absolute height and length by using the `S-index` ratio, but it makes it easier to compare sausage-ness directly.


```python
fig, ax = plt.subplots(figsize=(20, 18))

sns.barplot(ax=ax, data=df.sort_values('s_index', ascending=False), x='s_index', y='breed', color='#42b7bd')

# Vertical line at x=1
#ax.axvline(1, ls='--')

ax.set_title('Sausage-ness of dog breeds', fontsize=24, pad=30)
ax.set_ylabel('Breed', labelpad=20, fontsize=18)
ax.set_xlabel('Length / Height', labelpad=20, fontsize=18)


# Annotation with box
bbox_props = dict(boxstyle="rarrow,pad=0.6", fc="cyan", ec="#42b7bd", lw=2)
t = ax.text(1.35, 49, 'Approaching theoretical sausage ideal', bbox=bbox_props)

# Annotation with arrows
ax.annotate('Tall bois', xy=(0.993056, 49), xycoords='data', xytext=(1.1, 49), textcoords='data',
           arrowprops=dict(arrowstyle='->', color='red'))
ax.annotate('', xy=(0.850427, 50), xycoords='data', xytext=(1.15, 49), textcoords='data',
           arrowprops=dict(arrowstyle='->', color='red', connectionstyle='angle'))

plt.tight_layout()
plt.savefig('sausageness.png', dpi=300)
```


    
![Bar plot](/img/sausageness-bar.png)
    


Perhaps unsurprisingly, the Dachshund is the most sausage-y of sausage dogs, followed closely by the Basset Hound. I'd expected the Corgi to have a higher `S-index` than it does here. The Havanese isn't a breed I would normally classify as a sausage dog at first glance, but it goes to show how deceiving all that fur can be. 

It's also interesting to note that we have two tall bois in our sample. The Great Dane and the Standard Poodle are both taller than they are long.


<img src='https://upload.wikimedia.org/wikipedia/commons/thumb/e/e0/Dog_niemiecki_%C5%BC%C3%B3%C5%82ty_LM980.jpg/1024px-Dog_niemiecki_%C5%BC%C3%B3%C5%82ty_LM980.jpg' alt='Great Dane' width='400'>

_Credit: Lilly M via Wikipedia_

<img src='https://upload.wikimedia.org/wikipedia/commons/thumb/f/f8/Full_attention_%288067543690%29.jpg/1920px-Full_attention_%288067543690%29.jpg' alt='Standard Poodle' width='400'>

_Credit: Tim Wilson via Wikipedia_


Now you know which sausage dogs are more sausage-y, where other dogs lie on the sausage-ness (*cough* `S-index`) spectrum, and which dogs are tall bois. You're welcome.


### Things I Learned

This project is more than a little tongue-in-cheek, but I managed to get a surprising amount out of it.

#### Mid-range

My first instinct was to use mean or median height and length as a measure of central tendency, but I stopped short as sooon as I realised that I only had access to the minimum and maximum values. I Googled around and found [this thread](https://stats.stackexchange.com/questions/162818/if-i-only-have-a-range-is-it-acceptable-to-calculate-an-average-out-of-it) about the mid-range as an estimator of the mean. It's a pretty dismal statistic, but you work with what you have.

#### Seaborn

I haven't used Matplotlib and Seaborn in a while. Though I love Seaborn for abstracting away a lot of finicky styling decisions, I now realise I need to go through the Seaborn docs properly to understand how it interfaces with Matplotlib. Specifically, I should read up on figure-level and axes level Seaborn functions (in this notebook, the scatter plot and bar plot are figure-level and axes-level functions respectively).

Day 29 of [#100DaysToOffload](https://100daystooffload.com/)