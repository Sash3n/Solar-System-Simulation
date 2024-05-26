<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>3D Solar System Simulation</title>
    <style>
        body { margin: 0; }
        canvas { display: block; }
    </style>
</head>
<body>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/three.js/r128/three.min.js"></script>
        
    <script>
        // Setup scene
        const scene = new THREE.Scene();
        const camera = new THREE.PerspectiveCamera(75, window.innerWidth / window.innerHeight, 0.1, 1000);
        const renderer = new THREE.WebGLRenderer();
        renderer.setSize(window.innerWidth, window.innerHeight);
        document.body.appendChild(renderer.domElement);

        // Load textures
        const textureLoader = new THREE.TextureLoader();
        const sunTexture = textureLoader.load('sun.jpg');
        const mercuryTexture = textureLoader.load('mercury.jpg');
        const venusTexture = textureLoader.load('venus.jpg');
        const earthTexture = textureLoader.load('earth.jpg');
        const moonTexture = textureLoader.load('moon.jpg'); // Earth Moon texture
        const marsTexture = textureLoader.load('mars.jpg');
        const jupiterTexture = textureLoader.load('jupiter.jpg');
        const saturnTexture = textureLoader.load('saturn.jpg');
        const saturnRingTexture = textureLoader.load('saturnring.png');
        const uranusTexture = textureLoader.load('uranus.jpg');
        const uranusRingTexture = textureLoader.load('uranusring.png');
        const neptuneTexture = textureLoader.load('neptune.jpg');
        
        // Jupiter moon textures
        const europaTexture = textureLoader.load('europa.jpg');
        const callistoTexture = textureLoader.load('callisto.jpg');
        const ganymedeTexture = textureLoader.load('ganymede.jpg');
        
        // Saturn moon textures
        const enceladusTexture = textureLoader.load('enceladus.jpg');
        const tethysTexture = textureLoader.load('tethys.jpg');
        const titanTexture = textureLoader.load('titan.jpg');
         
        // Uranus moon textures
        const mirandaTexture = textureLoader.load('miranda.jpg');
        const arielTexture = textureLoader.load('ariel.jpg');
        const umbrielTexture = textureLoader.load('umbriel.jpg');
        
        // Neptune moon textures
        const thalassaTexture = textureLoader.load('thalassa.jpg');
        const despinaTexture = textureLoader.load('despina.jpg');

        // Load star background texture
        const starBackgroundTexture = textureLoader.load('stars.jpg');

        // Create star background
        const starBackgroundGeometry = new THREE.SphereGeometry(500, 60, 40);
        const starBackgroundMaterial = new THREE.MeshBasicMaterial({ map: starBackgroundTexture, side: THREE.BackSide });
        const starBackground = new THREE.Mesh(starBackgroundGeometry, starBackgroundMaterial);
        scene.add(starBackground);

        // Define the angle of rotation (in radians) relative to the orbital plane
        const ringRotationAngle = 26.7 * Math.PI / 180;

        const uranusRingRotationAngle = 98 * Math.PI / 180;

        // Define tilt angles for each planet's orbital plane (in degrees)
        const tiltAngles = {
            mercury: 7,   // Example tilt angle for Mercury
            venus: 3,     // Example tilt angle for Venus
            earth: 0,     // Earth's orbit is nearly flat
            mars: 1.85,   // Example tilt angle for Mars
            jupiter: 1.3, // Example tilt angle for Jupiter
            uranus: 0.8,  // Example tilt angle for Uranus
            neptune: 1.8  // Example tilt angle for Neptune
        };

        // Create Sun
        const sunGeometry = new THREE.SphereGeometry(2, 32, 32);
        const sunMaterial = new THREE.MeshBasicMaterial({ map: sunTexture });
        const sun = new THREE.Mesh(sunGeometry, sunMaterial);
        scene.add(sun);

        // Create Mercury
        const mercuryGeometry = new THREE.SphereGeometry(0.2, 32, 32);
        const mercuryMaterial = new THREE.MeshBasicMaterial({ map: mercuryTexture });
        const mercury = new THREE.Mesh(mercuryGeometry, mercuryMaterial);
        scene.add(mercury);

        // Create Venus
        const venusGeometry = new THREE.SphereGeometry(0.4, 32, 32);
        const venusMaterial = new THREE.MeshBasicMaterial({ map: venusTexture });
        const venus = new THREE.Mesh(venusGeometry, venusMaterial);
        scene.add(venus);

        // Create Earth
        const earthGeometry = new THREE.SphereGeometry(0.5, 32, 32);
        const earthMaterial = new THREE.MeshBasicMaterial({ map: earthTexture });
        const earth = new THREE.Mesh(earthGeometry, earthMaterial);
        scene.add(earth);

        // Create Moon for Earth
        const moonGeometry = new THREE.SphereGeometry(0.1, 32, 32);
        const moonMaterial = new THREE.MeshBasicMaterial({ map: moonTexture });
        const moon = new THREE.Mesh(moonGeometry, moonMaterial);
        // Position the moon relative to the earth
        earth.add(moon); // Add moon as a child of the earth

        // Create Mars
        const marsGeometry = new THREE.SphereGeometry(0.3, 32, 32);
        const marsMaterial = new THREE.MeshBasicMaterial({ map: marsTexture });
        const mars = new THREE.Mesh(marsGeometry, marsMaterial);
        scene.add(mars);

        // Create Jupiter
        const jupiterGeometry = new THREE.SphereGeometry(1, 32, 32);
        const jupiterMaterial = new THREE.MeshBasicMaterial({ map: jupiterTexture });
        const jupiter = new THREE.Mesh(jupiterGeometry, jupiterMaterial);
        scene.add(jupiter);

        // Create moons for Jupiter
        const europaGeometry = new THREE.SphereGeometry(0.15, 32, 32);
        const europaMaterial = new THREE.MeshBasicMaterial({ map: europaTexture });
        const europa = new THREE.Mesh(europaGeometry, europaMaterial);
        jupiter.add(europa);

        const callistoGeometry = new THREE.SphereGeometry(0.12, 32, 32);
        const callistoMaterial = new THREE.MeshBasicMaterial({ map: callistoTexture });
        const callisto = new THREE.Mesh(callistoGeometry, callistoMaterial);
        jupiter.add(callisto);

        const ganymedeGeometry = new THREE.SphereGeometry(0.1, 32, 32);
        const ganymedeMaterial = new THREE.MeshBasicMaterial({ map: ganymedeTexture });
        const ganymede = new THREE.Mesh(ganymedeGeometry, ganymedeMaterial);
        jupiter.add(ganymede);

        // Create Saturn
        const saturnGeometry = new THREE.SphereGeometry(0.9, 32, 32);
        const saturnMaterial = new THREE.MeshBasicMaterial({ map: saturnTexture });
        const saturn = new THREE.Mesh(saturnGeometry, saturnMaterial);
        scene.add(saturn);

        // Create Saturn's ring
        const saturnRingGeometry = new THREE.RingGeometry(1.2, 2, 32);
        const saturnRingMaterial = new THREE.MeshBasicMaterial({ map: saturnRingTexture, side: THREE.DoubleSide, transparent: true });
        const saturnRing = new THREE.Mesh(saturnRingGeometry, saturnRingMaterial);
        saturnRing.rotation.x = Math.PI / 2 + ringRotationAngle;
        saturn.add(saturnRing);

        
         // Create moons for Saturn
         const enceladusGeometry = new THREE.SphereGeometry(0.12, 32, 32);
        const enceladusMaterial = new THREE.MeshBasicMaterial({ map: enceladusTexture });
        const enceladus = new THREE.Mesh(enceladusGeometry, enceladusMaterial);
        saturn.add(enceladus);

        const tethysGeometry = new THREE.SphereGeometry(0.1, 32, 32);
        const tethysMaterial = new THREE.MeshBasicMaterial({ map: tethysTexture });
        const tethys = new THREE.Mesh(tethysGeometry, tethysMaterial);
        saturn.add(tethys);

        const titanGeometry = new THREE.SphereGeometry(0.08, 32, 32);
        const titanMaterial = new THREE.MeshBasicMaterial({ map: titanTexture });
        const titan = new THREE.Mesh(titanGeometry, titanMaterial);
        saturn.add(titan);

        // Create Uranus
        const uranusGeometry = new THREE.SphereGeometry(0.7, 32, 32);
        const uranusMaterial = new THREE.MeshBasicMaterial({ map: uranusTexture });
        const uranus = new THREE.Mesh(uranusGeometry, uranusMaterial);
        scene.add(uranus);

        // Create Uranus ring
        const uranusRingGeometry = new THREE.RingGeometry(1.2, 2, 32);
        const uranusRingMaterial = new THREE.MeshBasicMaterial({ map: uranusRingTexture, side: THREE.DoubleSide, transparent: true });
        const uranusRing = new THREE.Mesh(uranusRingGeometry, uranusRingMaterial);
        uranusRing.rotation.x = Math.PI / 2 + uranusRingRotationAngle;
        uranus.add(uranusRing);

        // Create moons for Uranus
        const mirandaGeometry = new THREE.SphereGeometry(0.1, 32, 32);
        const mirandaMaterial = new THREE.MeshBasicMaterial({ map: mirandaTexture });
        const miranda = new THREE.Mesh(mirandaGeometry, mirandaMaterial);
        uranus.add(miranda);

        const arielGeometry = new THREE.SphereGeometry(0.08, 32, 32);
        const arielMaterial = new THREE.MeshBasicMaterial({ map: arielTexture });
        const ariel = new THREE.Mesh(arielGeometry, arielMaterial);
        uranus.add(ariel);

        const umbrielGeometry = new THREE.SphereGeometry(0.06, 32, 32);
        const umbrielMaterial = new THREE.MeshBasicMaterial({ map: umbrielTexture });
        const umbriel = new THREE.Mesh(umbrielGeometry, umbrielMaterial);
        uranus.add(umbriel);

        // Create Neptune
        const neptuneGeometry = new THREE.SphereGeometry(0.6, 32, 32);
        const neptuneMaterial = new THREE.MeshBasicMaterial({ map: neptuneTexture });
        const neptune = new THREE.Mesh(neptuneGeometry, neptuneMaterial);
        scene.add(neptune);

        // Create moons for Neptune
        const thalassaGeometry = new THREE.SphereGeometry(0.08, 32, 32);
        const thalassaMaterial = new THREE.MeshBasicMaterial({ map: thalassaTexture });
        const thalassa = new THREE.Mesh(thalassaGeometry, thalassaMaterial);
        neptune.add(thalassa);

        const despinaGeometry = new THREE.SphereGeometry(0.06, 32, 32);
        const despinaMaterial = new THREE.MeshBasicMaterial({ map: despinaTexture });
        const despina = new THREE.Mesh(despinaGeometry, despinaMaterial);
        neptune.add(despina);

        camera.position.z = 50;

        renderer.setSize(window.innerWidth, window.innerHeight);
        document.body.appendChild(renderer.domElement);

        // Define raycaster and mouse vector
        const raycaster = new THREE.Raycaster();
        const mouse = new THREE.Vector2();

        // Event listener for mouse click
        document.addEventListener('mousedown', onMouseDown, false);

        function onMouseDown(event) {
        event.preventDefault();
    
        // Calculate mouse position in normalized device coordinates
        mouse.x = (event.clientX / window.innerWidth) * 2 - 1;
        mouse.y = -(event.clientY / window.innerHeight) * 2 + 1;
    
        // Update the picking ray with the camera and mouse position
        raycaster.setFromCamera(mouse, camera);
    
        // Calculate objects intersecting the picking ray
        const intersects = raycaster.intersectObjects(scene.children, true);
    
        // Check if any intersected object is a planet, moon, or the sun
        const clickableObjects = [sun, mercury, venus, earth, mars, moon, jupiter, europa, callisto, ganymede, saturn, enceladus, tethys, titan, uranus, miranda, ariel, umbriel, neptune, thalassa, despina];
        const isClickable = intersects.some(intersect => clickableObjects.includes(intersect.object));
    
        if (isClickable) {
            // Zoom in on the first intersected object (closest to the camera)
            camera.position.copy(intersects[0].point).sub(raycaster.ray.direction);
        }
        }


        // Event listener for mouse wheel (zoom)
        document.addEventListener('wheel', onDocumentMouseWheel, false);

        function onDocumentMouseWheel(event) {
            // Adjust camera position based on scroll direction
            camera.position.z += event.deltaY * 0.1;
        
            // Clamp camera position to avoid zooming in too close or going through the scene
            camera.position.z = Math.max(camera.position.z, 5);
            camera.position.z = Math.min(camera.position.z, 100);
        }
        
        // Function to calculate the position of a planet with a tilted orbit
        function calculateTiltedPosition(time, orbitRadius, tiltAngle) {
            // Calculate the position along the orbit without tilt
            const x = Math.cos(time * orbitRadius);
            const z = Math.sin(time * orbitRadius);
        
            // Apply tilt to the position
            const tiltedX = x * Math.cos(tiltAngle);
            const tiltedZ = z * Math.sin(tiltAngle);
        
            return { x: tiltedX, z: tiltedZ };
        }

        // Animate function
        function animate() {
            requestAnimationFrame(animate);
            const time = Date.now() * 0.0005;
        
            // Example orbit radius for all planets
            const orbitRadius = 0.1;
        
            // Update positions of planets with tilted orbits
            mercury.position.set(...Object.values(calculateTiltedPosition(time * 0.5, orbitRadius, tiltAngles.mercury)));
            venus.position.set(...Object.values(calculateTiltedPosition(time * 0.4, orbitRadius, tiltAngles.venus)));
            mars.position.set(...Object.values(calculateTiltedPosition(time * 0.2, orbitRadius, tiltAngles.mars)));
            jupiter.position.set(...Object.values(calculateTiltedPosition(time * 0.1, orbitRadius, tiltAngles.jupiter)));
            uranus.position.set(...Object.values(calculateTiltedPosition(time * 0.06, orbitRadius, tiltAngles.uranus)));
            neptune.position.set(...Object.values(calculateTiltedPosition(time * 0.05, orbitRadius, tiltAngles.neptune)));
        
            renderer.render(scene, camera);
        }

        // Render loop
        function animate() {
            requestAnimationFrame(animate);
            
            // Rotate the celestial objects around the sun
            const time = Date.now() * 0.0005;
            const orbitRadius = 15;
            
            mercury.position.x = Math.cos(time * 0.5) * orbitRadius;
            mercury.position.z = Math.sin(time * 0.5) * orbitRadius;

            venus.position.x = Math.cos(time * 0.4) * (orbitRadius + 3);
            venus.position.z = Math.sin(time * 0.4) * (orbitRadius + 3);

            earth.position.x = Math.cos(time * 0.3) * (orbitRadius + 6);
            earth.position.z = Math.sin(time * 0.3) * (orbitRadius + 6);

            mars.position.x = Math.cos(time * 0.2) * (orbitRadius + 9);
            mars.position.z = Math.sin(time * 0.2) * (orbitRadius + 9);

            jupiter.position.x = Math.cos(time * 0.1) * (orbitRadius + 12);
            jupiter.position.z = Math.sin(time * 0.1) * (orbitRadius + 12);

            saturn.position.x = Math.cos(time * 0.08) * (orbitRadius + 18);
            saturn.position.z = Math.sin(time * 0.08) * (orbitRadius + 18);

            uranus.position.x = Math.cos(time * 0.06) * (orbitRadius + 22);
            uranus.position.z = Math.sin(time * 0.06) * (orbitRadius + 22);

            neptune.position.x = Math.cos(time * 0.05) * (orbitRadius + 26);
            neptune.position.z = Math.sin(time * 0.05) * (orbitRadius + 26);

            // Rotate moons around their respective planets
            rotateMoons(time);

            renderer.render(scene, camera);
        }

        // Function to rotate moons around their respective planets
        function rotateMoons(time) {
            const moonOrbitRadius = 1.5;
            const moonOrbitSpeed = 0.02;

            // Rotation speed and radius for Earth's moon
            const earthMoonOrbitRadius = 1;
            const earthMoonOrbitSpeed = 0.03;

            // Calculate the position of Earth's moon
            moon.position.x = Math.cos(time * earthMoonOrbitSpeed) * earthMoonOrbitRadius;
            moon.position.z = Math.sin(time * earthMoonOrbitSpeed) * earthMoonOrbitRadius;

            europa.position.x = Math.cos(time * moonOrbitSpeed * 2) * moonOrbitRadius;
            europa.position.z = Math.sin(time * moonOrbitSpeed * 2) * moonOrbitRadius;

            callisto.position.x = Math.cos(time * moonOrbitSpeed * 1.5) * (moonOrbitRadius + 0.2);
            callisto.position.z = Math.sin(time * moonOrbitSpeed * 1.5) * (moonOrbitRadius + 0.2);

            ganymede.position.x = Math.cos(time * moonOrbitSpeed) * (moonOrbitRadius + 0.4);
            ganymede.position.z = Math.sin(time * moonOrbitSpeed) * (moonOrbitRadius + 0.4);

            enceladus.position.x = Math.cos(time * moonOrbitSpeed * 2) * (moonOrbitRadius + 0.5);
            enceladus.position.z = Math.sin(time * moonOrbitSpeed * 2) * (moonOrbitRadius + 0.5);

            tethys.position.x = Math.cos(time * moonOrbitSpeed * 1.5) * (moonOrbitRadius + 0.3);
            tethys.position.z = Math.sin(time * moonOrbitSpeed * 1.5) * (moonOrbitRadius + 0.3);

            titan.position.x = Math.cos(time * moonOrbitSpeed) * (moonOrbitRadius + 0.6);
            titan.position.z = Math.sin(time * moonOrbitSpeed) * (moonOrbitRadius + 0.6);

            miranda.position.x = Math.cos(time * moonOrbitSpeed * 2) * (moonOrbitRadius + 0.3);
            miranda.position.z = Math.sin(time * moonOrbitSpeed * 2) * (moonOrbitRadius + 0.3);

            ariel.position.x = Math.cos(time * moonOrbitSpeed * 1.5) * (moonOrbitRadius + 0.2);
            ariel.position.z = Math.sin(time * moonOrbitSpeed * 1.5) * (moonOrbitRadius + 0.2);

            umbriel.position.x = Math.cos(time * moonOrbitSpeed) * (moonOrbitRadius + 0.4);
            umbriel.position.z = Math.sin(time * moonOrbitSpeed) * (moonOrbitRadius + 0.4);

            thalassa.position.x = Math.cos(time * moonOrbitSpeed * 2) * (moonOrbitRadius + 0.2);
            thalassa.position.z = Math.sin(time * moonOrbitSpeed * 2) * (moonOrbitRadius + 0.2);

            despina.position.x = Math.cos(time * moonOrbitSpeed * 1.5) * (moonOrbitRadius + 0.3);
            despina.position.z = Math.sin(time * moonOrbitSpeed * 1.5) * (moonOrbitRadius + 0.3);

            // Rotating the planets around their axes
            sun.rotation.y += 0.01;
            mercury.rotation.y += 0.01;
            venus.rotation.y += 0.01;
            earth.rotation.y += 0.01;
            mars.rotation.y += 0.01;
            jupiter.rotation.y += 0.01;
            saturn.rotation.y += 0.01;
 
            uranus.rotation.y += 0.01;
         
            neptune.rotation.y += 0.01;

        }

        animate();

    </script>
</body>
</html>
