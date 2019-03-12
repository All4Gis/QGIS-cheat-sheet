QGIS 3 cheat sheet
==================

Cheat sheet for PyQgis

Table of Contents
-----------------

-  `UI <#ui>`__
-  `Canvas <#canvas>`__
-  `Processing algorithms <#processing-algorithms>`__
-  `TOC <#toc>`__
-  `Advanced TOC <#advanced-toc>`__
-  `Layers <#layers>`__
-  `Settings <#settings>`__
-  `ToolBars <#toolbars>`__
-  `Menus <#menus>`__
-  `Common PyQGIS functions <#common-pyqgis-functions>`__
-  `Sources <#sources>`__

UI
--

**Change Look & Feel**

::

    app = QApplication.instance()
    qss_file = open(r"style.qss").read()
    app.setStyleSheet(qss_file)

**Change Icon and Title**

::

    icon = QIcon(r"logo.png")
    iface.mainWindow().setWindowIcon(icon)  
    iface.mainWindow().setWindowTitle("CNE")

Canvas
------

**Access Canvas**

::

    canvas = iface.mapCanvas()

**Change Canvas color**

::

    iface.mapCanvas().setCanvasColor(QtCore.Qt.black)       
    iface.mapCanvas().refresh()

↑ `Back to top <#table-of-contents>`__

Processing algorithms
---------------------

**Get algorithms list**

::

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

**Get algorithms Help**

Random selection

::

    import processing
    processing.algorithmHelp("qgis:randomselection")

**How many algorithms are there?**

::

    len(QgsApplication.processingRegistry().algorithms())

↑ `Back to top <#table-of-contents>`__

TOC
---

**Access checked Layers**

::

    iface.mapCanvas().layers()

**Obtain Layers name**

::

    canvas = iface.mapCanvas()
    layers = [canvas.layer(i) for i in range(canvas.layerCount())]
    layers_names = [ layer.name() for layer in layers ]
    print "layers TOC = ", layers_names

    or

    layers = [layer for layer in QgsProject.instance().mapLayers().values()]

**Add vector layer**

::

    layer = iface.addVectorLayer("input.shp", "name", "ogr")
    if not layer:
      print "Layer failed to load!"

**Find layer by name**

::

    from qgis.core import QgsProject
    layer = QgsProject.instance().mapLayersByName("name")[0]
    print layer.name()

**Set Active layer**

::

    from qgis.core import QgsProject
    layer = QgsProject.instance().mapLayersByName("name")[0]
    iface.setActiveLayer(layer)

**Remove all layers**

::

    QgsProject.instance().removeAllMapLayers()

**Remove Contextual menu**

::

    ltv = iface.layerTreeView()
    ltv.setMenuProvider( None ) 

**See the CRS**

::

    for layer in QgsProject().instance().mapLayers().values():   
        crs = layer.crs().authid()
        layer.setName(layer.name() + ' (' + crs + ')')

**Load all layers from GeoPackage**

::

    fileName = "sample.gpkg"
    layer = QgsVectorLayer(fileName,"test","ogr")
    subLayers =layer.dataProvider().subLayers()

    for subLayer in subLayers:
        name = subLayer.split('!!::!!')[1]
        uri = "%s|layername=%s" % (fileName, name,)
        #Create layer
        sub_vlayer = QgsVectorLayer(uri, name, 'ogr')
        #Add layer to map
        QgsProject.instance().addMapLayer(sub_vlayer)

↑ `Back to top <#table-of-contents>`__

Advanced TOC
------------

**Root node**

::

    root = QgsProject.instance().layerTreeRoot()
    print (root)
    print (root.children())

**Access the first child node**

::

    child0 = root.children()[0]
    print (child0)
    print (type(child0))
    print (isinstance(child0, QgsLayerTreeLayer))
    print (child0.parent())

**Find groups and nodes**

::

    for child in root.children():
      if isinstance(child, QgsLayerTreeGroup):
        print ("- group: " + child.name())
      elif isinstance(child, QgsLayerTreeLayer):
        print ("- layer: " + child.name() + "  ID: " + child.layerId())

**Find group by name**

::

    print (root.findGroup("Name"))

**Add layer**

::

    layer1 = QgsVectorLayer("Point?crs=EPSG:4326", "Layer 1", "memory")
    QgsProject.instance().addMapLayer(layer1, False)
    node_layer1 = root.addLayer(layer1)

**Add Group**

::

    node_group2 = QgsLayerTreeGroup("Group 2")
    root.addChildNode(node_group2)

**Add Node** root.removeChildNode(node\_group2) root.removeLayer(layer1)

**Move Node**

::

    cloned_group1 = node_group1.clone()
    root.insertChildNode(0, cloned_group1)
    root.removeChildNode(node_group1)

**Rename None**

::

    node_group1.setName("Group X")
    node_layer2.setName("Layer X")

**Changing visibility**

::

    print (node_group1.isVisible())
    node_group1.setItemVisibilityChecked(False)
    node_layer2.setItemVisibilityChecked(False)

**Expand Node**

::

    print (node_group1.isExpanded())
    node_group1.setExpanded(False)

**Hidden Node Trick**

::

    model = iface.layerTreeView().layerTreeModel()
    ltv = iface.layerTreeView()
    root = QgsProject.instance().layerTreeRoot()

    layer = QgsProject.instance().mapLayersByName(u'Name')[0]
    node=root.findLayer( layer.id())

    index = model.node2index( node )
    ltv.setRowHidden( index.row(), index.parent(), True )
    node.setCustomProperty( 'nodeHidden', 'true')
    ltv.setCurrentIndex(model.node2index(root))  

**Node Signals**

::

    def onWillAddChildren(node, indexFrom, indexTo):
      print ("WILL ADD", node, indexFrom, indexTo)

    def onAddedChildren(node, indexFrom, indexTo):
      print ("ADDED", node, indexFrom, indexTo)

    root.willAddChildren.connect(onWillAddChildren)
    root.addedChildren.connect(onAddedChildren)

**Create new TOC**

::

    from qgis.gui import *
    root = QgsProject.instance().layerTreeRoot()
    model = QgsLayerTreeModel(root)
    view = QgsLayerTreeView()
    view.setModel(model)
    view.show()

↑ `Back to top <#table-of-contents>`__

Layers
------

**Add Vector layer**

::

    layer = iface.addVectorLayer("/path/to/shapefile/file.shp", "layer name you like", "ogr")

**Get Active Layer**

::

    layer = iface.activeLayer()

**Show methods**

::

    dir(layer)

**Get Features**

::

    for f in layer.getFeatures():
        print (f)

**Get Geometry**

::

     for f in layer.getFeatures():
      geom = f.geometry()
      print ('%s, %s, %f, %f' % (f['NAME'], f['USE'],
         geom.asPoint().y(), geom.asPoint().x()))

**Hide a field column**

::

    def fieldVisibility (layer,fname):
      setup = QgsEditorWidgetSetup('Hidden', {})
      for i, column in enumerate(layer.fields()):
        if column.name()==fname:
          layer.setEditorWidgetSetup(idx, setup)
        break
        else:
          continue
          

**Move geometry**

::

    geom = feat.geometry()
    geom.translate(100, 100)
    feat.setGeometry(geom)

**Adding new feature**

::

    iface.openFeatureForm(iface.activeLayer(), QgsFeature(), False)

**Layer from WKT**

::

    layer = QgsVectorLayer('Polygon?crs=epsg:4326', 'Mississippi', 'memory')
    pr = layer.dataProvider()
    poly = QgsFeature()
    geom = QgsGeometry.fromWkt("POLYGON ((-88.82 34.99,-88.0934.89,-88.39 30.34,-89.57 30.18,-89.73 31,-91.63 30.99,-90.8732.37,-91.23 33.44,-90.93 34.23,-90.30 34.99,-88.82 34.99))")
    poly.setGeometry(geom)
    pr.addFeatures([poly])
    layer.updateExtents()
    QgsProject.instance().addMapLayers([layer])

↑ `Back to top <#table-of-contents>`__

Settings
--------

**Get QSettings list**

::

    from PyQt5.QtCore import QSettings
    qs = QSettings()

    for k in sorted(qs.allKeys()):
        print (k)

↑ `Back to top <#table-of-contents>`__

ToolBars
--------

**Remove Toolbar**

::

    toolbar = iface.helpToolBar()   
    parent = toolbar.parentWidget()
    parent.removeToolBar(toolbar)

    #and add again

    parent.addToolBar(toolbar)

**Remove actions toolbar**

::

    actions = iface.attributesToolBar().actions()
    iface.attributesToolBar().clear()
    iface.attributesToolBar().addAction(actions[4])
    iface.attributesToolBar().addAction(actions[3])

↑ `Back to top <#table-of-contents>`__

Menus
-----

**Remove Menu**

::

    menu = iface.helpMenu() 
    menubar = menu.parentWidget()
    menubar.removeAction(menu.menuAction())

    #and add again
    menubar.addAction(menu.menuAction())

↑ `Back to top <#table-of-contents>`__

Common PyQGIS functions
-----------------------

https://github.com/boundlessgeo/lib-qgis-commons

https://github.com/inasafe/inasafe/tree/master/safe/utilities

https://docs.qgis.org/testing/en/docs/pyqgis\_developer\_cookbook/index.html

https://pcjericks.github.io/py-gdalogr-cookbook/index.html

http://www.green-forums.info/greenlib/geolibrary/Lawhead%20J/QGIS%20Python%20Programming%20Cookbook.%2020%20%2852%29/QGIS%20Python%20Programming%20Cookboo%20-%20Lawhead%20J.pdf
(a commercial 2015 book, but seems to have been released for download)

↑ `Back to top <#table-of-contents>`__

Sources
-------

http://docs.qgis.org/testing/en/docs/pyqgis_developer_cookbook/

http://qgis.org/api/

https://stackoverflow.com/questions/tagged/qgis

↑ `Back to top <#table-of-contents>`__
