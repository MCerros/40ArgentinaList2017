![Imagen](https://github.com/MCerros/40ArgentinaList2017/blob/master/40PrincipalesArgentina.png)

# 40ArgentinaList2017
Web scraping de todos los resultados de la lista de los 40 Principales argentina para el año 2017

Contexto:

La finalidad de este proyecto es obtener los datos de la lista de los 40 Principales Argentina, del año 2017 (45 semanas). Se obtiene los datos de quienes son los artistas y su posición en la lista, de esta manera se puede saber que artistas tienen más de una canción en la misma semana, o quienes aparecen más semanas en la lista en los que va del 2017.

Periodo: 1 de enero de 2017 a 12 de noviembre de 2017 (Semana 45, ultima actualización disponible)

Proceso de extracción: El proceso de web scraping se ha realizado con R, mediante la librería rvest.

Agradecimientos: Este repositorio está basado en un trabajo de “Auroritas” donde el proceso de extracción de datos es mucho más extenso. Puede encontrar más información sobre “Autoritas” en el siguiente enlace http://autoritas.net/.
Los datos han sido extraídos de “Los 40” Argentina, radio que pertenece al Grupo de medios Prisa. http://los40.com.ar/lista40/

Inspiración: La música es un mercado fascinante, que anualmente mueve elevadas cifras de dinero en todo el mundo. Sin embargo, la llegada de internet ha cambiado de lleno la forma en que se consume música y como esta es rentabilizada económicamente. Además, las nuevas tecnológicas han permitido que muchos nuevos artistas puedan darse a conocer de formas que antes no se existían. 
Por medio de la extracción de datos de este ranking semanal, podemos ver dentro del año 2017 la evolución de los gustos musicales de la población de Argentina. De esta forma evitamos tener que realizar encuestas o procesos más complejos.
Se ha elegido el ranking del país austral debido a que, quien escribe (MCerros), conoce un poco más de cerca dicho mercado.
Dicho repositorio ha sido realizado como parte de la PEC2, de la asignatura “Tipología y ciclo de vida de los datos” de la UOC.

Licencia: la licencia escogida es "Unlicensed" o sea, sin licencia, en otras palabras, este repositorio no tiene ningún tipo de licencia asociada. De esta manera cualquier persona u organización puede usar, copiar, o modificar este código sin ningún problema.

Archivos:

LICENSE: Licencia
CodigoR y Codigo.R: Código escrito en R
DatasetLista.csv: Datos extraídos.
Artists.csv: Ejemplo de lista semanal

Ejemplo DatasetLista (Graph)
```
> graph
                source            target       type    weight
1              Shakira            Maluma undirected 40.000000
2        Ariana Grande       Nicki Minaj undirected 13.333333
3           The Weeknd         Daft Punk undirected 10.000000
4       Cédric Gervais      Chris Willis undirected  8.000000
5       Cédric Gervais      David Guetta undirected  8.000000
6         Chris Willis      David Guetta undirected  8.000000
7             J Balvin               BIA undirected  6.666667
8             J Balvin Pharrell Williams undirected  6.666667
...
```
Ejemplo Código
```
library(rvest)

countries <- ("Argentina")

for (country in 1:length(countries)) {
  for (year in 2017) {
    pos <- NULL
    artist <- NULL
    graph <- data.frame(s=character(), t=character(), ty=character(), w=double())
    lst <- data.frame(p=integer(), n=integer(), a=character())
    
    for (week in 1:45) {
      print(paste("Processing year", year, "and week", week, "for", countries[country]))
      url <- paste("http://los40.com.ar/lista40/", year, "/", week, sep="")
      html <- read_html(url)
      divs <- html %>% html_nodes("div.article.estirar") 
      pos <- divs %>% html_nodes("span.posicion") %>% html_text()
      artists <- divs %>% html_nodes("div.info_grupo h4") %>% html_text()
      if (length(artists)==0) { break }
      
      for (a in 1:40) {
        artist <- strsplit(artists[a], ";")[[1]]
        p <- pos[a]
        if (is.na(p)) { break }
        lst <- rbind(lst, data.frame(as.numeric(p), length(artist), gsub(" Â»", "", artists[a])))
        
        if (length(artist)>1) {
          for (i in 1:length(artist)) {
            if (length(artist)>=(i+1)) {
              for (j in (i+1):length(artist)) {
                s <- gsub(" Â»", "", artist[i])
                t <- gsub(" Â»", "", artist[j])
                w <- 40 / as.numeric(p)
                graph <- rbind(graph, data.frame(s, t, "undirected", w))
              }
            }
          }
        }
      }
      
      Sys.sleep(0.5)
    }
    colnames(graph) <- c("source", "target", "type", "weight")
    write.csv(graph, paste("los40-ar-", year, "-graph.csv", sep=""), row.names=FALSE)
    
    colnames(lst) <- c("position", "num_authors", "authors")
    write.csv(lst, paste("los40-ar-", year, "-list.csv", sep=""), row.names=FALSE)
  }
}
```
Ejemplo aplicación del código:

```
[1] "Processing year 2017 and week 1 for Argentina"
[1] "Processing year 2017 and week 2 for Argentina"
[1] "Processing year 2017 and week 3 for Argentina"
[1] "Processing year 2017 and week 4 for Argentina"
[1] "Processing year 2017 and week 5 for Argentina"
[1] "Processing year 2017 and week 6 for Argentina"
[1] "Processing year 2017 and week 7 for Argentina"
[1] "Processing year 2017 and week 8 for Argentina"
[1] "Processing year 2017 and week 9 for Argentina"
[1] "Processing year 2017 and week 10 for Argentina"
...
```
