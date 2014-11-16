facebookcrawling
================

getting details from facebook user
require(pixmap)

# download small profile picture of each friend
dir.create("photos")
for (i in 1:length(friends.id))
  download.file(paste("http://graph.facebook.com", friends.id[i], "picture", sep="/"), 
                destfile=paste("photos/",friends.id[i],".jpg",sep=""))
system('for i in `ls photos/*.jpg`; do j=${i%.*}; convert $j.jpg $j.pnm; done', wait=TRUE)

# customized node plotting function
makeNodeDrawFunction <- function(x) {
 force(x)
 function(node, ur, attrs, radConv) {
    photo <- read.pnm(paste("photos/", x, ".pnm", sep=""))
    nc <- getNodeCenter(node)
    addlogo(photo, c(getX(nc)-25, getX(nc)+25), c(getY(nc)-25, getY(nc)+25))
  }
}
drawFuns <- apply(as.array(friends.id), 1, makeNodeDrawFunction)

# a graph with photos
pdf(file="facebook2.pdf", width=25, height=25)
  attrs <- list(node=list(shape="box", width=0.75, height=0.75))
  plot(g, "neato", attrs=attrs, drawNode=drawFuns)
dev.off()
