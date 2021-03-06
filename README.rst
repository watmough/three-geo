three-geo
===================

**three-geo** is a three.js based geographic visualization library.  Using
three-geo, we can build satellite-textured 3D terrain models in near
real-time by simply specifying GPS coordinates anywhere on the globe.

Credits: this library has been made possible by many OSS geographic libraries
(such as @mapbox/\*, @turf/\*, d3) in npm, and the Mapbox tile API.

Demo
----

As a demo application of the three-geo library, we introduce **examples/geo-viewer** (`live <https://w3reality.github.io/three-geo/examples/geo-viewer/io/index.html>`__ | `source code <https://github.com/w3reality/three-geo/tree/master/examples/geo-viewer>`__).

This app supports features such as 1) on-demand 3D terrain generation, 2) camera syncing with Leaflet, 3) terrain interaction and measurement, 4) auto camera orbiting, etc.

-
  .. image:: https://w3reality.github.io/three-geo/examples/img/5.jpg
     :target: https://w3reality.github.io/three-geo/examples/geo-viewer/io/index.html
-
  .. image:: https://w3reality.github.io/three-geo/examples/img/2.jpg
     :target: https://w3reality.github.io/three-geo/examples/geo-viewer/io/index.html

Setup
-----

**Installation**

.. code::
   
   $ npm install three-geo

**Loading**

Via script tags: use ``ThreeGeo`` after

.. code::

   <script src="three.min.js"></script>
   <script src="dist/three-geo.min.js"></script>

Via the ES6 module system: use ``ThreeGeo`` after
   
.. code::

   import ThreeGeo from 'three-geo/src';

Usage
-----

Here's an example to build a terrain located at GPS coordinates (46.5763,
7.9904) in a 5 km radius circle.  The terrain's zoom resolution is set to 12
(where the highest value supported is 14).

For standalone tests, use **examples/simple-viewer** (`source code <https://github.com/w3reality/three-geo/tree/master/examples/simple-viewer>`__).

.. code::

    const tgeo = new ThreeGeo({
        tokenMapbox: '********', // <---- set your Mapbox API token here
    });

    // params: [lat, lng], terrain's radius (km), zoom resolution, callbacks
    // Beware the value of radius; radius > 5.0 (km) could trigger huge number of tile API calls!!
    tgeo.getTerrain([46.5763, 7.9904], 5.0, 12, {
        onRgbDem: (meshes) => { // your implementation when the terrain's geometry is obtained
            meshes.forEach((mesh) => { scene.add(mesh); });
            render(); // now render scene after dem meshes are added
        },
        onSatelliteMat: (mesh) => { // your implementation when terrain's satellite texture is obtained
            render(); // now render scene after dem material (satellite texture) is applifed
        },
    });

.. image:: https://w3reality.github.io/three-geo/examples/img/1.jpg

API
---

**ThreeGeo**

- **constructor(opts={})**

  Create a ThreeGeo instance with parameters.
  
  - ``opts.tokenMapbox``\="" **string** Mapbox API token.  This must be provided.
  - ``opts.unitsSide``\=1.0 **number** The side length of the square that fits the terrain in WebGL space.

- **getTerrain(latlng, radius, zoom, callbacks={})**


  - ``latlng`` **Array<number>** GPS coordinates of the form: [latitude, longitude].
  - ``radius`` **number** The radius of the circle that fits the terrain.
  - ``zoom`` **number (integer)** Zoom resolution of the tiles in the terrain.
    Select from {12, 13, 14}, where 14 is the highest value supported.
  - ``callbacks.onRgbDem`` **function (meshes) {}** Implement this to request the geometry of the terrain.  Called when the entire terrain's geometry is obtained.
      - ``meshes`` **Array<THREE.Mesh>** All the meshes belonging to the terrain.
  - ``callbacks.onSatelliteMat`` **function (mesh) {}** Implement this to request the satellite textures of the terrain.  Called when the satellite texture of each mesh belonging to the terrain is obtained.
      - ``mesh`` **THREE.Mesh** One of the meshes that's part of the terrain.


Build
-----

.. code::

   $ npm install  # set up build tools
   $ npm run build  # generate module files in lib/
