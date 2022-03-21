# plot1cell: a package for advanced single cell data visualization

This package allows users to visualize the single cell data on the R objects or output files generated by the popular tools such as Seurat. It is currently under active development. 

## Installation
plot1cell R package can be easily installed from Github using devtools. Please make sure you have installed Seurat4.0, circlize and ComplexHeatmap packages.
```
devtools::install_github("HaojiaWu/plot1cell")
```

## Usage
We provide some example codes to help generate figures on user's own Seurat object. The Seurat object input to plot1cell should be a final object with completed clustering and cell type annotation. If a seurat object is not available, we suggest to use the demo data from Satija's lab (https://satijalab.org/seurat/articles/integration_introduction.html). To demonstrate the plotting functions in plot1cell, we re-created an Seurat object from our recent paper <a href="https://www.pnas.org/doi/10.1073/pnas.2005477117">Kirita et al, PNAS 2020</a> by integrating the count matrices we uploaded to GEO (GSE139107).
```
iri.integrated <- Install.example() 

# Please note that this Seurat object is just for demo purpose and 
# does not exactly the same as we published on PNAS.
# It take about 2 hours to run in a linux server with 500GB RAM and 32 CPU cores.
# You can skip this step and use your own Seurat object instead
```

### 1. Circlize plot to visualize cell clustering and meta data
```
###check and see the meta data info on your Seurat object
colnames(iri.integrated@meta.data)  

###Generate data for ploting
circ_data <- prepare_circlize_data(iri.integrated, scale = 0.8 )
set.seed(1234)
cluster_colors<-rand_color(length(levels(iri.integrated)))
group_colors<-rand_color(length(table(iri.integrated$Group)))
rep_colors<-group_colors<-rand_color(length(table(iri.integrated$orig.ident)))

###plot and save figures
png(filename =  'circlize_plot.png', width = 6, height = 6,units = 'in', res = 300)
plot_circlize(circ_data,do.label = T, pt.size = 0.01, col.use = cluster_colors ,bg.color = 'white', kde2d.n = 200)
add_track(circ_data, group = "Group", colors = group_colors) ## can change the meta data on your data
add_track(circ_data, group = "orig.ident",colors = rep_colors) ## can change the meta data on your data
dev.off()
```
![alt text](https://github.com/HaojiaWu/Plot1cell/blob/master/circlize_plot.png) <br />

### 2. Dotplot to show gene expression across groups
Here is an example to use plot1cell to show one gene expression across different cell types breakdown by group.
```
png(filename =  'dotplot_single.png', width = 4, height = 6,units = 'in', res = 100)
complex_dotplot_single(seu_obj = iri.integrated, feature = "Havcr1",groupby = "Group")
dev.off()
```
![alt text](https://github.com/HaojiaWu/Plot1cell/blob/master/dotplot_single.png) <br />
plot1cell allows visualization of multiple genes in dotplot format too. Here is an example.
```
png(filename =  'dotplot_multiple.png', width = 10, height = 4,units = 'in', res = 300)
complex_dotplot_multiple(seu_obj = iri.integrated, features = c("Slc34a1","Slc7a13","Havcr1","Krt20","Vcam1"),groupby = "Group", celltypes = c("PTS1" ,   "PTS2"  ,  "PTS3"  ,  "NewPT1" , "NewPT2"))
dev.off()
```
![alt text](https://github.com/HaojiaWu/Plot1cell/blob/master/dotplot_multiple.png) <br />

### 3. Violin plot to show gene expression across groups
#### One gene/one group violin plot:
```
png(filename =  'vlnplot_single.png', width = 4, height = 6,units = 'in', res = 300)
complex_vlnplot_single(iri.integrated, feature = "Havcr1", groups = "Group",celltypes   = c("PTS1" ,   "PTS2"  ,  "PTS3"  ,  "NewPT1" , "NewPT2"))
dev.off()
```
![alt text](https://github.com/HaojiaWu/Plot1cell/blob/master/vlnplot_single.png) <br />

#### One genes/multiple groups violin plot:
```
png(filename =  'vlnplot_multiple.png', width = 8, height = 6,units = 'in', res = 300)
complex_vlnplot_single(iri.integrated, feature = "Havcr1", groups = c("Group","Replicates"),celltypes   = c("PTS1" ,   "PTS2"  ,  "PTS3"  ,  "NewPT1" , "NewPT2"))
dev.off()
```
![alt text](https://github.com/HaojiaWu/Plot1cell/blob/master/vlnplot_multiple.png) <br />

Note that the Replicates group here is for demo purpose. This is not the mouse ID as reported in our original paper.

#### Multiple genes/multiple groups.
The violin plot will look too messy in this scenario so it is not included in plot1cell. It would highly recommend to use the complex_dot_plot instead. <br />
For scRNA-seq studies with higher complexity, the complex_vlnplot_single function also allows split the group by another group in the meta data with the argument "split.by". Since the demo dataset doesn't have this complexity, examples are not included here. Users can refer to our recent DKD dataset with multiple treatments/two timepoints (the PCT violin graph in <a href="https://humphreyslab.com/SingleCell/">K.I.T.</a>)



