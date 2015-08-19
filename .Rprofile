.Last <- function() {
    curtime <- Sys.time();
    seshlen <- floor(as.numeric(curtime-.Start.SysTime));
    if (seshlen >= 10) {
        cat("\nYou worked for ", seshlen, " seconds.\nIt is now ",format(curtime),". Come back soon!\n\n", sep="");
        if (seshlen >= 600) {
            cat("Saving since you worked over 10 minutes. Filename: ");
            savefilename <- readline();
            save.image(file=ifelse(nchar(savefilename)>0), savefilename, ".RData");
        }
    }
}

.First <- function() .Start.SysTime <<- Sys.time();

local({

    username <- Sys.getenv("LOGNAME")[1];
    suroster <- c('root');    ## superusers you might realistically run a shell as.
    criticalpackages <- c("utils", "methods"); ## always loaded.
    optionalpackages <- c(c("MASS", "datasets", "stats", "grDevices", "graphics"), ## base R, plus MASS.
                          c("data.table", "Matrix"), ## advanced data types.
                          c("ggplot2", "hexbin"), ## advanced graphics.
                          c("rpart", "randomForest", "nnet", "tsne", "cluster", "stringdist", "lme4", "mgcv"), ## advanced models.
                          c("shiny"), ## AJAX
                          c("iptools", "scales") ## utilities.
                          ); ## optionalpackages are loaded unless username %in% suroster

    options(
        menu.graphics=FALSE, expressions=1e5, warn=1, warning.length=8170,
        max.print=5e2, warnPartialMatchArgs=TRUE, warnPartialMatchAttr=TRUE, warnPartialMatchDollar=TRUE,
        keep.source=TRUE, keep.source.pkgs=TRUE, continue=">>> ", add.smooth=FALSE, stringsAsFactors=FALSE,
        pkgType="both", repos=c(CRAN="http://cran.rstudio.com/"), ## get up-to-date source packages when possible
        quietly=TRUE, verbose=FALSE,
        datatable.integer64="double", datatable.verbose=FALSE);

    ## read terminal size by invoking subsidiary (not really) interactive bash with window-bound checking enabled.
    ## the COLUMNS variable is not automatically set in bash without these options.
    options(width=as.numeric(system("bash -i -O checkwinsize -c 'echo $COLUMNS'", intern=TRUE)), length=99999);

    if (username %in% suroster) {
        cat("\n\n",
            "****************************************************************************\n",
            "***** YOU ARE RUNNING AS ROOT. ARE YOU SURE YOU WANT TO BE DOING THIS? *****\n",
            "*****                                                                  *****\n",
            "*****  Disabling optional packages (quasi-vanilla) for installations.  *****\n",
            "****************************************************************************\n\n\n");
    }

    ## state version; load default packages; list loaded packages and warn about failures.
    cat(paste0(version[c("version.string", "nickname", "platform")], collapse=", "), ".", sep="");
    loadedpackages <- suppressPackageStartupMessages({
        sapply(criticalpackages, function(x) suppressWarnings(require(x, character.only=TRUE)))});
    if (any(!loadedpackages)) cat("\n", paste("!!! FAILED TO LOAD:",names(criticalpackages)[!loadedpackages], collapse="\n"));
    if (!username %in% suroster) {
        if (any(!loadedpackages)) {
            cat("\n!!! Since a critical package failed to load, we're skipping the optional ones. Fix it first.");
        } else {
            cat(".. Loading optional packages... ");
            loadedpackages <- suppressPackageStartupMessages({
                sapply(optionalpackages, function(x) suppressWarnings(require(x, character.only=TRUE)))});
            cat(paste(names(optionalpackages)[loadedpackages], collapse=", "));
            if (any(!loadedpackages)) cat("\n", paste("! FAILED TO LOAD:",names(optionalpackages)[!loadedpackages], collapse="\n"));
            cat("Done!");
        }
    }
    cat("\n");

    ## check for packages which can be updated -- this takes a bit of time.
    oldpackages <- rownames(old.packages());
    options(error=utils::recover); ## this is put here so the user can break the above line without being yelled at.
    if (exists('oldpackages') && length(oldpackages)) {
        cat(paste0("There are ", length(oldpackages), " updated packages.\n"));
        if (username %in% suroster) {
            cat("Since you are root, we're running update.packages(). Cancel if you don't like that.\n");
            update.packages();
            cat("\nHopefully that worked. Either way, you should probably q() now and restart...\n");
        }
    }

});
