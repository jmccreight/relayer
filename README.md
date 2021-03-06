
<!-- README.md is generated from README.Rmd. Please edit that file -->

# relayer

Rethinking layers in ggplot2, written by Claus O. Wilke

## Installation

    devtools::install_github("clauswilke/relayer")

This is an experimental package. Use at your own risk. API is not
stable. No user support provided.

## Examples

Load required packages:

``` r
library(ggplot2) # version 2.3.0 required
library(dplyr)
library(relayer)
```

Make a layer with its own fill scale:

``` r
df1 <- data.frame(x = c(1, 2, 3))

ggplot(df1, aes(x = x)) +
  geom_tile(aes(fill = x, y = 2)) +
  geom_tile(aes(fill2 = x, y = 1)) %>% rename_geom_aes(new_aes = c("fill" = "fill2")) +
  scale_colour_viridis_c(aesthetics = "fill", guide = "legend", name = "viridis A", option = "A") +
  scale_colour_viridis_c(aesthetics = "fill2", guide = "legend", name = "viridis D")
#> Warning: Ignoring unknown aesthetics: fill2
```

![](man/figures/README-unnamed-chunk-3-1.png)<!-- -->

Make several layers with their own color scales:

``` r
# make color scale shortcut
scale_distiller <- function(aesthetics, palette, name, ...) {
  scale_colour_distiller(
    aesthetics = aesthetics,
    palette = palette,
    name = name, ...,
    guide = guide_legend(order = palette))
}

# subset data
iris_setosa <- filter(iris, Species == "setosa")
iris_versicolor <- filter(iris, Species == "versicolor")
iris_virginica <- filter(iris, Species == "virginica")

# plot
ggplot(mapping = aes(Sepal.Length, Sepal.Width)) +
  (geom_point(data = iris_setosa, aes(colour1 = Sepal.Width)) %>%
     rename_geom_aes(new_aes = c("colour" = "colour1"))) +
  (geom_point(data = iris_versicolor, aes(colour2 = Sepal.Width)) %>%
     rename_geom_aes(new_aes = c("colour" = "colour2"))) +
  (geom_point(data = iris_virginica, aes(colour3 = Sepal.Width)) %>%
     rename_geom_aes(new_aes = c("colour" = "colour3"))) +
  facet_wrap(~Species) +
  scale_distiller("colour1", 5, "setosa") +
  scale_distiller("colour2", 6, "versicolor") +
  scale_distiller("colour3", 7, "virginica") +
  theme_bw() +
  theme(legend.position = "bottom")
#> Warning: Ignoring unknown aesthetics: colour1
#> Warning: Ignoring unknown aesthetics: colour2
#> Warning: Ignoring unknown aesthetics: colour3
```

![](man/figures/README-unnamed-chunk-4-1.png)<!-- -->

Use multiple color scales within a single layer. Note the difference to
the previous example: Now the three color scales are jointly trained to
the union of the data values.

``` r
ggplot(iris, aes(Sepal.Length, Sepal.Width)) +
  geom_point(aes(col1 = Sepal.Width, col2 = Sepal.Width, col3 = Sepal.Width, group = Species)) %>%
    rename_geom_aes(
      new_aes = c("colour" = "col1", "colour" = "col2", "colour" = "col3"),
      aes(colour = case_when(group == 1 ~ col1, group == 2 ~ col2, TRUE ~ col3))
    ) +
    facet_wrap(~Species) +
    scale_distiller("col1", 5, "setosa") +
    scale_distiller("col2", 6, "versicolor") +
    scale_distiller("col3", 7, "virginica") +
    theme_bw() + theme(legend.position = "bottom", legend.key.width = grid::unit(6, "pt"))
#> Warning: Ignoring unknown aesthetics: col1, col2, col3
```

![](man/figures/README-unnamed-chunk-5-1.png)<!-- -->

Swap `colour` and `fill` aesthetics in a geom:

``` r
ggplot(iris, aes(Sepal.Length, fill = Species)) +
  geom_density(colour = "gray70", size = 2, alpha = 0.7) %>%
  rename_geom_aes(new_aes = c("fill" = "colour", "colour" = "fill"))
```

![](man/figures/README-unnamed-chunk-6-1.png)<!-- -->
