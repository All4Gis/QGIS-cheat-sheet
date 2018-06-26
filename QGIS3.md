
# QGIS 3 cheat sheet

Cheat sheet for PyQgis

Processing algorithms 
---

__Get algorithms list__

	for alg in QgsApplication.processingRegistry().algorithms():
		print("{}:{} --> {}".format(alg.provider().name(), alg.name(), alg.displayName()))
		
	def alglist():
	  s = ''
	  for i in QgsApplication.processingRegistry().algorithms():
	    l = i.displayName().ljust(50, "-")
	    r = i.id()
	    s += '{}--->{}\n'.format(l, r)
	  print(s)

	alglist()

__Get algorithms Help__

Random selection

	import processing
	processing.algorithmHelp("qgis:randomselection")

