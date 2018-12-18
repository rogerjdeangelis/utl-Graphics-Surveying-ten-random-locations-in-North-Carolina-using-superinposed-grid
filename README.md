# utl-Graphics-Surveying-ten-random-locations-in-North-Carolina-using-superinposed-grid
Graphics Surveying ten random locations in North Carolina using superinposed grid.
    Graphics Surveying ten random locations in North Carolina using superinposed grid

    graphics output
    https://tinyurl.com/y7nfzjgv
    https://github.com/rogerjdeangelis/utl-Graphics-Surveying-ten-random-locations-in-North-Carolina-using-superinposed-grid/blob/master/utl_draGrd

    github
    https://tinyurl.com/ybd7w3yu
    https://github.com/rogerjdeangelis/utl-Graphics-Surveying-ten-random-locations-in-North-Carolina-using-superinposed-grid

    see for commented solution
    https://tinyurl.com/ycoqfggk
    https://gis.stackexchange.com/questions/88830/overlay-a-spatial-polygon-with-a-grid-and-check-in-which-grid-element-specific-c

    INPUT
    =====

     North Carolina shape files

     Five random points from from the afganistan shapfile


    EXAMPLE OUTPUT (Produced using the classic editor with mm/mm and o scripting)
    -----------------------------------------------------------------------------
    Method applied to North Carolina


      Y |                        AFGANISTAN
        |
     30 +  +---------------------------------------------------+
        |  |            |           ,-.^._        |        _   |
        |  |            |         .' |    `-.     |      ,' ;  |
        |  |           /`-.  ,----'  |      `-.   _  ,-.,'  `  |
        |  | 17     _.' | `--'    18 | 19      `-'|'-'      ;20|
     25 +  +-------:-------------------------o-------------;---+
        |  |       ,'   |o           |            |        ;   |
        |  |      :     |            | o          |      ,--  `|
        |  |      :     |     o      |            |o     ;     |
        |  | 13   :     | 14         | 15         |      :   16|
     20 +  +------;--------------------------------------:-----+
        |  |     (      |            |            |      ;     |
        |  |      `-.   |            |          o |    ,'      |
        |  |        ;   |            |            l   :        |
        |  | 9    .'    | 10         | 11        .-._,'     12 |
     15 +  +----.'---------o---------------------`.------------+
        |  | _.'        |            |          .__;           |
        |  | `._        |         o  |         ;  |            |
        |  |    `.      |            |        :   |            |
        |  | 5    `.    | 6        ,..__,---._; 7 | 8          |
     10 +  +--------`-.__---------:----------------------------+
        |  |            |`.--.____;  |            |            |
        |  |            |            |            |            |
        |  |            |            |            |            |
        |  | 1          | 2          | 3          | 4          |
      5 +  +---------------------------------------------------+
        |
        ---+------------+------------+------------+------------+-
           0           20           40           60           80


    WORK.WANT total obs=18

      CAPTURE_

      Simple feature collection with 11 features and 1 field
      geometry type:  POINT
      dimension:      XY
      bbox:           xmin: 345206.1 ymin: 3835756 xmax: 931545.1 ymax: 4049716
      epsg (SRID):    32617
      proj4string:    +proj=utm +zone=17 +datum=WGS84 +units=m +no_defs

      First 5 features:

      Random 5 grids for your survey

                  Random Point
      GRID ID    Shapfile Coords

        6      POINT (934894 3961902)
       11      POINT (440900 3922309)
       16      POINT (726931 3944919)
       15      POINT (681940 3962142)
       14      POINT (764622 4000460)


    PROCESS
    =======

    proc datasets lib=work;
      delete want;
    run;quit;

    %utlfkil(d:/xpt/draGrd.xpt);

    %utl_submit_r64("
    library(sf);
    library(dplyr);
    library(SASxport);
    library(ggplot2);
    nc <- st_read(system.file('shape/nc.shp', package = 'sf')) %>%
      st_transform(32617);
    pts <- st_sample(nc, size = 10) %>% st_sf;
    grid_50 <- st_make_grid(nc, cellsize = c(50000, 50000)) %>%
      st_sf(grid_id = 1:length(.));
    grid_lab <- st_centroid(grid_50) %>% cbind(st_coordinates(.));
    pdf(file='d:/pdf/utl_draGrd.pdf');
    ggplot() +
      geom_sf(data = nc, fill = 'white', lwd = 0.05) +
      geom_sf(data = pts, color = 'red', size = 1.7) +
      geom_sf(data = grid_50, fill = 'transparent', lwd = 0.3) +
      geom_text(data = grid_lab, aes(x = X, y = Y, label = grid_id), size = 2) +
      coord_sf(datum = NA)  +
      labs(x = '') +
      labs(y = '');
    pdf();
    pts %>% st_join(grid_50, join = st_intersects);
    want<-as.data.frame(capture.output(pts %>% st_join(grid_50, join = st_intersects)));
    str(want);
    want[] <- lapply(want, function(x) if(is.factor(x)) as.character(x) else x);
    write.xport(want,file='d:/xpt/draGrd.xpt');
    ");

    libname xpt xport "d:/xpt/draGrd.xpt";
    data want;
      set xpt.want;
    run;quit;

    proc print data=want;
    run;quit;


    OUTPUT
    ======

      CAPTURE_

      Simple feature collection with 12 features and 1 field
      geometry type:  POINT
      dimension:      XY
      bbox:           xmin: 290586.4 ymin: 3785157 xmax: 924560.4 ymax: 4001593
      epsg (SRID):    32617
      proj4string:    +proj=utm +zone=17 +datum=WGS84 +units=m +no_defs
      First 10 features:
         grid_id                 geometry
      1       77 POINT (600509.7 3973447)
      2       47 POINT (806395.1 3896252)
      3       66 POINT (924560.4 3951329)
      4       57 POINT (492248.1 3925348)
      5       61 POINT (665064.7 3907225)
      6       36 POINT (290586.4 3892125)
      7       83 POINT (908671.9 3984816)
      8       78 POINT (663824.5 4001593)
      9       29   POINT (750540 3814475)
      10      27 POINT (663015.7 3837623)


