# Group Ruru B README

##### Zhicheng Ye;Shuo Feng;Yigang Park;Zelda Reid;Haiqiang Liu

## `Download Rstudio`
[Rstudio][https://www.rstudio.com/products/rstudio/download/]

To see the graph, you had better to make sure that you have the latest stable version of R.


## `Install package`
If you don't have the package following that, you will need to install it first

> install.packages("tidyverse")

> install.packages("dypyr")

> install.packages("ggwordcloud")



#### Load the package

```{r}
library(tidyverse)
library(ggwordcloud)
library(dplyr)
```



## `Download data`

#### Read the data

```{r}
Sentimentdata<-read.csv(file = "sentiment-data.csv",head = TRUE)
```
> Download the "sentiment-data.csv"
> 
> https://canvas.auckland.ac.nz/courses/75946/assignments/298345



## `Run the code`

#### Rename each colume

```{r}
names(Sentimentdata)<-c("OwnFeeling","StudentsFeeling","TeacherFeeling")
```


#### Unify word separators

```{r}
Sentimentdata$OwnFeeling<-gsub('.',',', Sentimentdata$OwnFeeling,fixed = TRUE)
Sentimentdata$OwnFeeling<-gsub('*','', Sentimentdata$OwnFeeling,fixed = TRUE)
Sentimentdata$OwnFeeling<-gsub('、',',', Sentimentdata$OwnFeeling,fixed = TRUE)
Sentimentdata$OwnFeeling<-gsub(',',',', Sentimentdata$OwnFeeling,fixed = TRUE)
```

> If there is any error in gsub(), add `Sys.setlocale('LC_ALL','C')` before run the code


#### Uppercase letters are converted to lowercase letters

```{r}
Sentimentdata$OwnFeeling<-tolower(Sentimentdata$OwnFeeling)
Sentimentdata$StudentsFeeling<-tolower(Sentimentdata$StudentsFeeling)
Sentimentdata$TeacherFeeling<-tolower(Sentimentdata$TeacherFeeling)
```

#### SelfFeeling
##### Delete NA

```{r}
Sentimentdata1<-Sentimentdata%>%
  separate(OwnFeeling,sep = ',',c('Own_First','Own_Second','Own_Third','Own_Forth'))
Sentimentdata1<-na.omit(Sentimentdata1)
```
##### Delete Emojis

```{r}
Sentimentdata1<-Sentimentdata1%>%
  filter(Own_Third != ',')
```

##### Remove all Spaces

```{r}
Sentimentdata1$Own_First<-gsub(' ','', Sentimentdata1$Own_First,fixed = TRUE)
Sentimentdata1$Own_Second<-gsub(' ','', Sentimentdata1$Own_Second,fixed = TRUE)
Sentimentdata1$Own_Third<-gsub(' ','', Sentimentdata1$Own_Third,fixed = TRUE)
Sentimentdata1$Own_Forth<-gsub(' ','', Sentimentdata1$Own_Forth,fixed = TRUE)
```

##### Merge Cells

```{r}
Sentimentdata1_gather<-Sentimentdata1%>%
  gather('OwnFeeling','Words',-StudentsFeeling,-TeacherFeeling)
```

##### Deep Clean data

```{r}
Sentimentdata1_gather$Words<-gsub("ing","ed",Sentimentdata1_gather$Words,fixed = TRUE)
Sentimentdata1_gather$Words<-gsub("ment","ed",Sentimentdata1_gather$Words,fixed = TRUE)
Sentimentdata1_gather$Words<-gsub("se","ze",Sentimentdata1_gather$Words,fixed = TRUE)
Sentimentdata1_gather<-Sentimentdata1_gather%>%
  summarise(stren_len=str_length(Words),Words)
Sentimentdata1_gather<-Sentimentdata1_gather%>%
  filter(stren_len>1)%>%
  filter(stren_len<15)
```

##### Count the frequency of words

```{r}
Own_Words<-as.data.frame.array(table(Sentimentdata1_gather$Words))
names(Own_Words)<-c('Frequency')
```

#### StudentsFeeling

```{r}
Sentimentdata$StudentsFeeling<-gsub('*','', Sentimentdata$StudentsFeeling,fixed = TRUE)
Sentimentdata$StudentsFeeling<-gsub(' ','', Sentimentdata$StudentsFeeling,fixed = TRUE)
Sentimentdata$StudentsFeeling<-gsub('ing','ed', Sentimentdata$StudentsFeeling,fixed = TRUE)
Sentimentdata$StudentsFeeling<-gsub('ment','ed', Sentimentdata$StudentsFeeling,fixed = TRUE)
Sentimentdata$StudentsFeeling<-gsub('ness','s', Sentimentdata$StudentsFeeling,fixed = TRUE)

Sentimentdata2_gather<-Sentimentdata%>%
  summarise(stren_len=str_length(StudentsFeeling),StudentsFeeling)
Sentimentdata2_gather<-Sentimentdata2_gather%>%
  filter(stren_len>1)%>%
  filter(stren_len<15) 

Students_words<-as.data.frame.array(table(Sentimentdata2_gather$StudentsFeeling))
names(Students_words)<-c('Frequency')
```

### TeacherFeeling

```{r}
Sentimentdata$TeacherFeeling<-gsub('*','', Sentimentdata$TeacherFeeling,fixed = TRUE)
Sentimentdata$TeacherFeeling<-gsub(' ','', Sentimentdata$TeacherFeeling,fixed = TRUE)
Sentimentdata$TeacherFeeling<-gsub('ing','ed', Sentimentdata$TeacherFeeling,fixed = TRUE)
Sentimentdata$TeacherFeeling<-gsub('ment','ed', Sentimentdata$TeacherFeeling,fixed = TRUE)

Sentimentdata3_gather<-Sentimentdata%>%
  summarise(stren_len=str_length(TeacherFeeling),TeacherFeeling)
Sentimentdata3_gather<-Sentimentdata3_gather%>%
  filter(stren_len>1)%>%
  filter(stren_len<15) 
Teacher_words<-as.data.frame.array(table(Sentimentdata3_gather$TeacherFeeling))
names(Teacher_words)<-c('Frequency')
```

### Output the data

```{r}
write.csv(Own_Words,"SelfFeeling.csv",row.names=TRUE)
write.csv(Students_words,"StudentFeeling.csv",row.names=TRUE)
write.csv(Teacher_words,"TeacherFeeling.csv",row.names=TRUE)
```

> Make sure upload `tidyverse` package
> 
> If you did not upload `tidyverse` package, install it first.
> run the code `install.packages("tidyverse")`
> 
> run the code `library("tidyverse")`
>

##### Make sure load the package
###### We mainly use the package ggwordcloud to draw the graph, make sure that you have installed it successfully
> library(tidyverse)
> 
> library(ggwordcloud)
> 
> library(dplyr)


###### Help of `ggwordcloud` package
[ggwordcloud][https://cran.r-project.org/web/packages/ggwordcloud/vignettes/ggwordcloud.html]


##### Running this code can get 3 data files. 
> "SelfFeeling.csv" is the data of question 1
> 
> "StudentFeeling.csv" is the data of question 2
> 
> "TeacherFeeling.csv"is the data of question3.


### Read the cleaned data

```{r}
SelfFeeling<-read.csv("SelfFeeling.csv")
StudentFeeling<-read.csv("StudentFeeling.csv")
TeacherFeeling<-read.csv("TeacherFeeling.csv")

set.seed(42)
```

> csv files should be put in the same folder 
> 
> 42 is the answer to everything 



### Rename the columns
### Prepare for the outer merging
### Merge three tables
### Remove the NA data by setting them to 0
```{r}

StudentFeeling<-StudentFeeling%>%rename(words=X,FreqStu=Frequency)
TeacherFeeling<-TeacherFeeling%>%rename(words=X,FreqTeach=Frequency)

Feelings<-merge(StudentFeeling,TeacherFeeling,all=TRUE)

Feelings$FreqStu[is.na(Feelings$FreqStu)]<-0
Feelings$FreqTeach[is.na(Feelings$FreqTeach)]<-0
```


```{r}
Feelings<-Feelings%>%
  gather(key = "type",value = "Freq",-words)%>%
  arrange(desc(Freq))
```

### Put two word clouds into one picture file

```{r}
p1<-ggplot(
  Feelings,
  aes(
    label = words, size = Freq,
    color = Freq)) +
  geom_text_wordcloud_area(
    eccentricity = 1,
    aes(angle =45*sample(-2:2, nrow(Feelings),
                          replace = TRUE,prob = c(1,1,4,1,1)))
    rm_outside = TRUE) +
  scale_size_area(max_size =25) +
  theme_minimal() +
  scale_color_gradient(low = "blue", high = "red")+
  facet_wrap(~type)

ggsave("Group_Ruru_B_Graphic_0.png",width = 8,height = 5)
png("Group_Ruru_B_Graphic_0.png",width = 1200,height = 1000,units = "px")
p1
dev.off()

```

#### How to dfind the path of "chicken.png"
> This path is the path of "chicken.png" in your computer. 
>
>  All you need to do is find "chicken.png" (it should be in the folder just downloaded).     
>  
>  Right-click to find the attribute.


#### Mask png file should be put in the same folder
> if you can not see the graph, try the "refresh" button






## `Compare the graphic image, and enjoy it :chicken:`

![Group_Ruru_B_Graphic](https://user-images.githubusercontent.com/109815710/180497716-2aa3480f-f148-4624-bacc-0f9f8d6f4f0f.png)




