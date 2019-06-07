
# QGIS 3 cheat sheet

Cheat sheet for PyQgis

## Table of Contents

- [UI](#ui)
- [Canvas](#canvas)
- [Decorators](#decorators)
- [Processing algorithms](#processing-algorithms)
- [TOC](#toc)
- [Advanced TOC](#advanced-toc)
- [Layers](#layers)
- [Settings](#settings)
- [ToolBars](#toolbars)
- [Menus](#menus)
- [Sources](#sources)


UI
---
__Change Look & Feel__

	from qgis.PyQt.QtWidgets import QApplication

	app = QApplication.instance()
	qss_file = open(r"/path/to/style/file.qss").read()
	app.setStyleSheet(qss_file)

__Change Icon and Title__

	from qgis.PyQt.QtGui import QIcon

	icon = QIcon(r"/path/to/logo/file.png")
	iface.mainWindow().setWindowIcon(icon)  
	iface.mainWindow().setWindowTitle("My QGIS")

Canvas
---
__Access Canvas__

	from qgis.utils import iface

	canvas = iface.mapCanvas()

__Change Canvas color__

	from qgis.PyQt.QtCore import Qt

	iface.mapCanvas().setCanvasColor(Qt.black)    
	iface.mapCanvas().refresh()

&uparrow; [Back to top](#table-of-contents)

Decorators
---
__CopyRight__


	from qgis.PyQt.Qt import QTextDocument
	from qgis.PyQt.QtGui import QFont

	mQFont = "Sans Serif"
	mQFontsize = 9
	mLabelQString = "© QGIS 2019"
	mMarginHorizontal = 0
	mMarginVertical = 0
	mLabelQColor = "#FF0000"

	INCHES_TO_MM = 0.0393700787402 # 1 millimeter = 0.0393700787402 inches
	case = 2

	def add_copyright(p, text, x_offset, y_offset):
	p.translate( xOffset , yOffset  )
	text.drawContents(p)
	p.setWorldTransform( p.worldTransform() )

	def _on_render_complete(p):
	deviceHeight = p.device().height() # Get paint device height on which this painter is currently painting
	deviceWidth  = p.device().width() # Get paint device width on which this painter is currently painting
	# Create new container for structured rich text
	text = QTextDocument()
	font = QFont()
	font.setFamily(mQFont)
	font.setPointSize(int(mQFontsize))
	text.setDefaultFont(font)
	style = "<style type=\"text/css\"> p {color: " + mLabelQColor + "}</style>"
	text.setHtml( style + "<p>" + mLabelQString + "</p>" )
	# Text Size
	size = text.size()

	# RenderMillimeters
	pixelsInchX  = p.device().logicalDpiX()
	pixelsInchY  = p.device().logicalDpiY()
	xOffset  = pixelsInchX  * INCHES_TO_MM * int(mMarginHorizontal)
	yOffset  = pixelsInchY  * INCHES_TO_MM * int(mMarginVertical)

	# Calculate positions
	if case == 0:
	    # Top Left
	    add_copyright(p, text, xOffset, yOffset)

	elif case == 1:
	    # Bottom Left
	    yOffset = deviceHeight - yOffset - size.height()
	    add_copyright(p, text, xOffset, yOffset)

	elif case == 2:
	    # Top Right
	    xOffset  = deviceWidth  - xOffset - size.width()
	    add_copyright(p, text, xOffset, yOffset)

	elif case == 3: 
	    # Bottom Right
	    yOffset  = deviceHeight - yOffset - size.height()
	    xOffset  = deviceWidth  - xOffset - size.width()
	    add_copyright(p, text, xOffset, yOffset)

	elif case == 4:
	    # Top Center
	    xOffset = deviceWidth / 2
	    add_copyright(p, text, xOffset, yOffset)

	else:
	    # Bottom Center
	    yOffset = deviceHeight - yOffset - size.height()
	    xOffset = deviceWidth / 2
	    add_copyright(p, text, xOffset, yOffset)

	# Emitted when the canvas has rendered
	iface.mapCanvas().renderComplete.connect(_on_render_complete)
	# Repaint the canvas map
	iface.mapCanvas().refresh()


&uparrow; [Back to top](#table-of-contents)

Processing algorithms 
---

__Get algorithms list__

    from qgis.core import QgsApplication

    for alg in QgsApplication.processingRegistry().algorithms():
        print("{}:{} --> {}".format(alg.provider().name(), alg.name(), alg.displayName()))

    # or 

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


__How many algorithms are there?__

    from qgis.core import QgsApplication

    len(QgsApplication.processingRegistry().algorithms())

__How many providers are there?__

    from qgis.core import QgsApplication

    len(QgsApplication.processingRegistry().providers())

__How many Expressions are there?__

    from qgis.core import QgsExpression

    len(QgsExpression.Functions()) 
	
&uparrow; [Back to top](#table-of-contents)

TOC
---

__Access checked Layers__

    from qgis.utils import iface

    iface.mapCanvas().layers()

__Obtain Layers name__

    canvas = iface.mapCanvas()
    layers = [canvas.layer(i) for i in range(canvas.layerCount())]
    layers_names = [ layer.name() for layer in layers ]
    print("layers TOC = ", layers_names)

    # or

    layers = [layer for layer in QgsProject.instance().mapLayers().values()]


__Add vector layer__

    layer = iface.addVectorLayer("/path/to/shapefile/file.shp", "layer name you like", "ogr")
    if not layer:
        print("Layer failed to load!")

__Find layer by name__

    from qgis.core import QgsProject

    layer = QgsProject.instance().mapLayersByName("layer name you like")[0]
    print(layer.name())

__Set Active layer__

    from qgis.core import QgsProject

    layer = QgsProject.instance().mapLayersByName("layer name you like")[0]
    iface.setActiveLayer(layer)

__Remove all layers__

    from qgis.core import QgsProject

    QgsProject.instance().removeAllMapLayers()

__Remove Contextual menu__

    ltv = iface.layerTreeView()
    ltv.setMenuProvider( None ) 	

__See the CRS__

    from qgis.core import QgsProject

    for layer in QgsProject().instance().mapLayers().values():   
        crs = layer.crs().authid()
        layer.setName('{} ({})'.format(layer.name(), crs))

__Set the CRS__

    from qgis.core import QgsProject, QgsCoordinateReferenceSystem

    for layer in QgsProject().instance().mapLayers().values():
        layer.setCrs(QgsCoordinateReferenceSystem(4326, QgsCoordinateReferenceSystem.EpsgCrsId))


__Load all layers from GeoPackage__

    from qgis.core import QgsVectorLayer, QgsProject

    fileName = "/path/to/gpkg/file.gpkg"
    layer = QgsVectorLayer(fileName,"test","ogr")
    subLayers =layer.dataProvider().subLayers()

    for subLayer in subLayers:
        name = subLayer.split('!!::!!')[1]
        uri = "%s|layername=%s" % (fileName, name,)
        # Create layer
        sub_vlayer = QgsVectorLayer(uri, name, 'ogr')
        # Add layer to map
        QgsProject.instance().addMapLayer(sub_vlayer)

__Load tile layer (XYZ-Layer)__

    from qgis.core import QgsRasterLayer, QgsProject

    def loadXYZ(url, name):
        rasterLyr = QgsRasterLayer("type=xyz&url=" + url, name, "wms")
        QgsProject.instance().addMapLayer(rasterLyr)

    urlWithParams = 'type=xyz&url=https://a.tile.openstreetmap.org/%7Bz%7D/%7Bx%7D/%7By%7D.png&zmax=19&zmin=0&crs=EPSG3857'
    loadXYZ(urlWithParams, 'OpenStreetMap')
	
__Load WCS layer__

	import urllib.parse
	
	# Make WCS Uri
	def makeWCSuri( url, layer ):  
	    params = {  'dpiMode': 7 ,
			'identifier': layer,
			'url': url.split('?')[0]  } 

	    uri = urllib.parse.unquote( urllib.parse.urlencode(params)  )
	    return uri 

	### Raster layer from WCS
	rlayername = 'DEP3ElevationPrototype'
	wcsUri = makeWCSuri('https://elevation.nationalmap.gov/arcgis/services/3DEPElevation/ImageServer/WCSServer', rlayername )
	rlayer = QgsRasterLayer(wcsUri, 'DEP3ElevationPrototype', 'wcs')
	if not rlayer.isValid():
	  print ("Layer failed to load!")

&uparrow; [Back to top](#table-of-contents)


Advanced TOC
---

__Root node__

    from qgis.core import QgsProject

    root = QgsProject.instance().layerTreeRoot()
    print (root)
    print (root.children()
	
__Access the first child node__

    from qgis.core import QgsLayerTreeGroup, QgsLayerTreeLayer

    child0 = root.children()[0]
    print (child0)
    print (type(child0))
    print (isinstance(child0, QgsLayerTreeLayer))
    print (child0.parent())

__Find groups and nodes__

    from qgis.core import QgsLayerTreeGroup, QgsLayerTreeLayer

    for child in root.children():
        if isinstance(child, QgsLayerTreeGroup):
            print ("- group: " + child.name())
        elif isinstance(child, QgsLayerTreeLayer):
            print ("- layer: " + child.name() + "  ID: " + child.layerId())

    
__Find group by name__

	print (root.findGroup("Name"))

__Add layer__

    from qgis.core import QgsVectorLayer, QgsProject

    layer1 = QgsVectorLayer("Point?crs=EPSG:4326", "layer name you like", "memory")
    QgsProject.instance().addMapLayer(layer1, False)
    node_layer1 = root.addLayer(layer1)

__Add Group__

    from qgis.core import QgsLayerTreeGroup

    node_group2 = QgsLayerTreeGroup("Group 2")
    root.addChildNode(node_group2)

__Remove layer__

	root.removeLayer(layer1)

__Remove group__

	root.removeChildNode(node_group2)

__Move Node__

	cloned_group1 = node_group1.clone()
	root.insertChildNode(0, cloned_group1)
	root.removeChildNode(node_group1)
	
__Rename None__

	node_group1.setName("Group X")
	node_layer2.setName("Layer X")

__Changing visibility__

	print (node_group1.isVisible())
	node_group1.setItemVisibilityChecked(False)
	node_layer2.setItemVisibilityChecked(False)

__Expand Node__

	print (node_group1.isExpanded())
	node_group1.setExpanded(False)

__Hidden Node Trick__

    from qgis.core import QgsProject

    model = iface.layerTreeView().layerTreeModel()
    ltv = iface.layerTreeView()
    root = QgsProject.instance().layerTreeRoot()

    layer = QgsProject.instance().mapLayersByName('layer name you like')[0]
    node=root.findLayer( layer.id())

    index = model.node2index( node )
    ltv.setRowHidden( index.row(), index.parent(), True )
    node.setCustomProperty( 'nodeHidden', 'true')
    ltv.setCurrentIndex(model.node2index(root))   

__Node Signals__

	def onWillAddChildren(node, indexFrom, indexTo):
	  print ("WILL ADD", node, indexFrom, indexTo)

	def onAddedChildren(node, indexFrom, indexTo):
	  print ("ADDED", node, indexFrom, indexTo)

	root.willAddChildren.connect(onWillAddChildren)
	root.addedChildren.connect(onAddedChildren)

__Create new TOC__

    from qgis.core import QgsProject, QgsLayerTreeModel
    from qgis.gui import QgsLayerTreeView 
    
    root = QgsProject.instance().layerTreeRoot()
    model = QgsLayerTreeModel(root)
    view = QgsLayerTreeView()
    view.setModel(model)
    view.show()

&uparrow; [Back to top](#table-of-contents)


Layers
---

__Add Vector layer__

    from qgis.utils import iface

    layer = iface.addVectorLayer("/path/to/shapefile/file.shp", "layer name you like", "ogr")

__Get Active Layer__

	layer = iface.activeLayer()

__List All Layers__

    from qgis.core import QgsProject

    names = [layer.name() for layer in QgsProject.instance().mapLayers().values()]
	
__Show methods__

	dir(layer)
	
__Get Features__

	for f in layer.getFeatures():
		print (f)
  
 __Get Selected Features__

    selectedFeatures = layer.selectedFeatureIds()
    print(selectedFeatures)
	
 __Create memory layer from selected features__
    
    memory_layer = layer.materialize(QgsFeatureRequest().setFilterFids(layer.selectedFeatureIds()))
    QgsProject.instance().addMapLayer(memory_layer)
    
 __Get Geometry__
 
	 for f in layer.getFeatures():
	  geom = f.geometry()
	  print ('%s, %s, %f, %f' % (f['NAME'], f['USE'],
		 geom.asPoint().y(), geom.asPoint().x()))
 
__Hide a field column__

    from qgis.core import QgsEditorWidgetSetup

    def fieldVisibility (layer,fname):
        setup = QgsEditorWidgetSetup('Hidden', {})
        for i, column in enumerate(layer.fields()):
            if column.name()==fname:
                layer.setEditorWidgetSetup(idx, setup)
                break
            else:
                continue
	      
__Move geometry__      

	geom = feat.geometry()
	geom.translate(100, 100)
	feat.setGeometry(geom)

__Adding new feature__

    from qgis.core import QgsFeature

    iface.openFeatureForm(iface.activeLayer(), QgsFeature(), False)

__Layer from WKT__

    from qgis.core import QgsVectorLayer, QgsFeature, QgsGeometry, QgsProject

    layer = QgsVectorLayer('Polygon?crs=epsg:4326', 'Mississippi', 'memory')
    pr = layer.dataProvider()
    poly = QgsFeature()
    geom = QgsGeometry.fromWkt("POLYGON ((-88.82 34.99,-88.0934.89,-88.39 30.34,-89.57 30.18,-89.73 31,-91.63 30.99,-90.8732.37,-91.23 33.44,-90.93 34.23,-90.30 34.99,-88.82 34.99))")
    poly.setGeometry(geom)
    pr.addFeatures([poly])
    layer.updateExtents()
    QgsProject.instance().addMapLayers([layer])

&uparrow; [Back to top](#table-of-contents)

Settings
---

__Get QSettings list__

	from qgis.PyQt.QtCore import QgsSettings
	qs = QSettings()

	for k in sorted(qs.allKeys()):
	    print (k)


&uparrow; [Back to top](#table-of-contents)

ToolBars
---

__Remove Toolbar__

    from qgis.utils import iface

    toolbar = iface.helpToolBar()   
    parent = toolbar.parentWidget()
    parent.removeToolBar(toolbar)

    # and add again
    parent.addToolBar(toolbar)

__Remove actions toolbar__

	actions = iface.attributesToolBar().actions()
	iface.attributesToolBar().clear()
	iface.attributesToolBar().addAction(actions[4])
	iface.attributesToolBar().addAction(actions[3])

&uparrow; [Back to top](#table-of-contents)

Menus
---

__Remove Menu__

    from qgis.utils import iface

    # for example Help Menu
    menu = iface.helpMenu() 
    menubar = menu.parentWidget()
    menubar.removeAction(menu.menuAction())

    # and add again
    menubar.addAction(menu.menuAction())


&uparrow; [Back to top](#table-of-contents)


Sources
---

https://qgis.org/pyqgis/

https://qgis.org/api/

https://docs.qgis.org/testing/en/docs/pyqgis_developer_cookbook/

https://stackoverflow.com/questions/tagged/qgis

https://raw.githubusercontent.com/klakar/QGIS_resources/master/collections/Geosupportsystem/python/qgis_basemaps.py

https://github.com/boundlessgeo/lib-qgis-commons

&uparrow; [Back to top](#table-of-contents)
