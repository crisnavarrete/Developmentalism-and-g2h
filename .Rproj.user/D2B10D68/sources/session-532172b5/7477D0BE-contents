# I plot 1: map ----

pacman::p_load(
  sf,
  dplyr,
  ggplot2,
  ggrepel,
  ggspatial,
  cowplot,
  rnaturalearth,
  rnaturalearthdata,
  tibble,
  devtools
)

# 1. shapes ----
temp <- tempdir()

zipfile <- file.path(temp, "Regiones.zip")

download.file(
  "https://www.bcn.cl/obtienearchivo?id=repositorio/10221/10398/2/Regiones.zip",
  destfile = zipfile,
  mode = "wb"
)

unzip(zipfile, exdir = temp)

shp <- list.files(
  temp,
  pattern = "\\.shp$",
  recursive = TRUE,
  full.names = TRUE
)

regions <- st_read(shp, quiet = TRUE)

# Select Magallanes
magallanes <- regions |>
  filter(Region == "Región de Magallanes y Antártica Chilena")


# Project locations (WGS84)
projects <- tibble::tribble(
  ~Project,          ~lon,        ~lat,
  "Haru Oni",        -70.57244,   -52.51230,
  "Cabo Negro",      -67.567851,  -54.930556,
  "Faro del Sur",    -70.955926,  -52.853130,
  "HNH",             -70.367824,  -52.302204,
  "H2 Magallanes",   -68.969362,  -52.262398
)

projects_sf <- st_as_sf(
  projects,
  coords = c("lon", "lat"),
  crs = 4326
)


# Project to UTM 19S
magallanes_utm <- st_transform(magallanes, 32719)
projects_utm   <- st_transform(projects_sf, 32719)


# Crop map to project extent
bbox <- st_bbox(projects_utm)

bbox["xmin"] <- bbox["xmin"] - 50000
bbox["xmax"] <- bbox["xmax"] + 50000
bbox["ymin"] <- bbox["ymin"] - 70000
bbox["ymax"] <- bbox["ymax"] + 70000

mag_crop <- st_crop(magallanes_utm, bbox)

# 2. Main map -----
main_map <-
  
  ggplot() +
  
  geom_sf(
    data = magallanes_utm,
    fill = "#F8F8F5",
    colour = "grey65",
    linewidth = 0.25
  ) +
  
  geom_sf(
    data = projects_utm,
    shape = 21,
    fill = "#C0392B",
    colour = "white",
    stroke = 0.6,
    size = 4
  ) +
  
  geom_label_repel(
    data = cbind(
      st_coordinates(projects_utm),
      st_drop_geometry(projects_utm)
    ),
    aes(
      X,
      Y,
      label = Project
    ),
    fill = "white",
    label.size = .2,
    size = 3.8,
    seed = 123,
    box.padding = .5,
    point.padding = .35,
    segment.size = .35,
    min.segment.length = 0
  ) +
  
  annotation_scale(
    location = "bl",
    width_hint = .20,
    text_cex = .9
  ) +
  
  annotation_north_arrow(
    location = "tl",
    style = north_arrow_fancy_orienteering,
    height = unit(1.0, "cm"),
    width  = unit(1.0, "cm")
  ) +
  
  coord_sf(expand = FALSE) +
  
  labs(
    x = NULL,
    y = NULL
  ) +
  
  theme_minimal(base_size = 13) +
  
  theme(
    
    panel.background =
      element_rect(
        fill = "#DDEEF9",
        colour = NA
      ),
    
    panel.grid.major =
      element_line(
        colour = "white",
        linewidth = .35
      ),
    
    panel.grid.minor =
      element_blank(),
    
    legend.position = "none",
    
    axis.title =
      element_blank(),
    
    axis.text =
      element_text(
        colour = "grey35",
        size = 11
      ),
    
    axis.ticks =
      element_line(
        colour = "grey40"
      ),
    
    plot.background =
      element_rect(
        fill = "white",
        colour = NA
      )
  )

main_map


# 2. Inset map----
chile <- ne_countries(
  country = "Chile",
  scale = "large",
  returnclass = "sf"
)

magallanes_wgs84 <- st_transform(magallanes_utm, 4326)

inset <-
  
  ggplot() +
  
  geom_sf(
    data = chile,
    fill = "grey96",
    colour = "grey55",
    linewidth = 0.20
  ) +
  
  geom_sf(
    data = magallanes_wgs84,
    fill = "#C0392B",
    colour = "#C0392B",
    linewidth = 0.25
  ) +
  
  coord_sf(
    xlim = c(-76, -66),
    ylim = c(-56.5, -17),
    expand = FALSE
  ) +
  
  theme_void() +
  
  theme(
    panel.background = element_rect(
      fill = "white",
      colour = "grey70",
      linewidth = 0.4
    ),
    plot.background = element_blank()
  )

# 3. Combine -----

final_map <-
  ggdraw() +

  draw_plot(
    main_map,
    x = 0,
    y = 0,
    width = 0.75,
    height = 1
  ) +
  
  draw_plot(
    inset,
    x = 0.75,
    y = 0.05,
    width = 0.25,
    height = 0.90
  )

final_map

# II plot 2: heatmap ----

pacman::p_load(
  tidyverse, haven, ggplot2, labelled, sjlabelled,
  ggrepel, scales, gapminder, socviz, readxl, dplyr, skimr,
  lubridate, changepoint, tidyr, forecast, viridis, here, purrr, reshape2,
  scales, ggbump, ggstream, RColorBrewer, fuzzyjoin, zoo, writexl,
  patchwork, hrbrthemes, changepoint.np, pheatmap, fmsb, igraph, here)


# Load the data
df <- read_excel(here("data","data_documental.xlsx"))

df <- df %>% 
  filter(Count >=3) %>% 
  select(-Count)


# 1. Descriptive Statistics ----
skimr::skim(df)

# 2. Thematic Aggregation ----

df_long <- df %>%
  pivot_longer(-Institution, names_to = "Action", values_to = "Mention") %>%
  mutate(Theme = str_extract(Action, "^A\\d+")) %>%
  group_by(Institution, Theme) %>%
  summarise(Mentions = sum(Mention, na.rm = TRUE), .groups = "drop")

# View the result
print(df_long)

df_long %>%
  group_by(Institution) %>%
  summarise(TotalMentions = sum(Mentions, na.rm = TRUE)) %>%
  arrange(desc(TotalMentions))

df_long <- df_long %>%
  mutate(
    Theme = factor(Theme),
    Theme = fct_reorder(Theme, as.numeric(str_remove(Theme, "A")))
  )
# Visualization: Bar chart
ggplot(df_long, aes(x = Theme, y = Mentions, fill = Theme)) +
  geom_col(show.legend = FALSE) +
  facet_wrap(~ Institution, scales = "free_y") +
  theme_minimal() +
  labs(title = "Theme Mentions per Institution", x = "Theme", y = "Mentions") +
  theme(axis.text.x = element_text(angle = 45, hjust = 1))


# 3. Heatmap ----

# Extract clean numeric theme index
df_long <- df_long %>%
  mutate(
    ThemeNumeric = as.numeric(str_remove(Theme, "A"))
  )

# Get unique, sorted Theme levels (no duplicates)
theme_levels <- df_long %>%
  distinct(Theme, ThemeNumeric) %>%
  arrange(ThemeNumeric) %>%
  pull(Theme)

# Institution order by total mentions
institution_order <- df_long %>%
  group_by(Institution) %>%
  summarise(TotalMentions = sum(Mentions, na.rm = TRUE)) %>%
  arrange(desc(TotalMentions)) %>%
  pull(Institution)

# Reorder Institution and Theme as factors
df_long <- df_long %>%
  mutate(
    Institution = factor(Institution, levels = rev(institution_order)),  # reverse Y-axis order
    Theme = factor(Theme, levels = theme_levels)
  )

# Plot the heatmap
ggplot(df_long, aes(x = Theme, y = Institution, fill = Mentions)) +
  geom_tile(color = "white") +
  scale_fill_viridis() +
  theme_minimal(base_size = 12) +
  labs(
    title = "Mentions Heatmap by Institution and Theme",
    x = "Theme",
    y = "Institution",
    fill = "Mentions"
  ) +
  theme(
    axis.text.x = element_text(angle = 45, hjust = 1),
    panel.grid = element_blank()
  ) 
