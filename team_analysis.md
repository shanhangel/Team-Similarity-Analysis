���ھ����������ӷ��Ƚ�
========================================================
## By Casper

*Just for fun*


�������package,��ȡcsv����

```r
library(ggplot2)
library(grid)
euro <- read.csv("euro.csv")
```

�޳�NA��

```r
euro <- subset(euro, goal!="NA")
```


```r
euro1 <- aggregate(euro[,-1], list(euro$team), mean)
rownames(euro1) <- euro1[,1]
euro2 <- euro1[,-1]
```

�������ж��������ƽ��ֵ���Լ�ÿ�ӵ��������ֵ

```r
mean_data <- colMeans(euro2)

for (i in 1:nrow(euro2)){
    euro2[i,] = euro2[i,]/mean_data
}
```

���������Cluster��Kֵȷ��

ȷ��K means Clusterֵ

accumulator for cost results
run kmeans for all clusters up to 7
Run kmeans for each level of i, allowing up to 100 iterations for convergence
Combine cluster number and cost together, write to df

```r
cost_df <- data.frame()

for(i in 1:(nrow(euro2)-1)){
    kmeans<- kmeans(euro2, centers = i)
    cost_df<- rbind(cost_df, cbind(i, kmeans$tot.withinss))
}
names(cost_df) <- c("cluster", "cost")
```

Plot

```r
ggplot(data=cost_df, aes(x=cluster, y=cost, group=1)) + 
    theme_gray() + 
    geom_line(colour = "darkgreen") +
    ggtitle("Reduction In Cost For Values of 'k'\n") +
    xlab("\nClusters") + 
    ylab("Within-Cluster Sum of Squares\n")
```

![plot of chunk unnamed-chunk-6](figure/unnamed-chunk-6-1.png) 

����ͼ���Կ�����K = 3�Ǹ�������ѡ��

```r
cluster <- kmeans(euro2, 3)

euro3 <- cbind(euro1[,1], euro2)
colnames(euro3[1]) <- "team"

euro4 <- cbind(1:8, euro2)
```


```r
plot(euro4[,2:8], pch = cluster$cluster, col = euro4[,1], 
     main = "Plot of Team Data by Cluster 1")
```

![plot of chunk unnamed-chunk-8](figure/unnamed-chunk-8-1.png) 


```r
plot(euro4[,9:15], pch = cluster$cluster, col = euro4[,1],
     main = "Plot of Team Data by Cluster 2")
```

![plot of chunk unnamed-chunk-9](figure/unnamed-chunk-9-1.png) 


���ݡ��������������ء����Ա����ġ��ƿء��͡�����ٽ��з���

```r
euro4$cluster <- factor(cluster$cluster)

grid.newpage()
vplayout <- function(x, y) viewport(layout.pos.row = x, layout.pos.col = y)
pushViewport(viewport(layout = grid.layout(2, 2)))
plot1 <- ggplot(euro4, aes(x=goal, y=shot, color=cluster, label=rownames(euro4)))
plot1 <- plot1 + geom_text()
plot1 <- plot1 + geom_text() + ggtitle("Offense")
plot2 <- ggplot(euro4, aes(x=steal, y=foul, color=cluster, label=rownames(euro4)))
plot2 <- plot2 + geom_text()
plot2 <- plot2 + geom_text() + ggtitle("Defense")
plot3 <- ggplot(euro4, aes(x=pass, y=possession, color=cluster, label=rownames(euro4)))
plot3 <- plot3 + geom_text()
plot3 <- plot3 + geom_text() + ggtitle("Control")
plot4 <- ggplot(euro4, aes(x=center, y=off_side, color=cluster, label=rownames(euro4)))
plot4 <- plot4 + geom_text()
plot4 <- plot4 + geom_text() + ggtitle("Style")

print(plot1, vp = vplayout(1, 1))
print(plot2, vp = vplayout(1, 2))
print(plot3, vp = vplayout(2, 1))
print(plot4, vp = vplayout(2, 2))
```

![plot of chunk unnamed-chunk-10](figure/unnamed-chunk-10-1.png) 