# Lumina.X
Space Solar System Design
npx create-react-app solar-system-app  
cd solar-system-app  
npm install three @react-three/fiber @react-three/drei
import React, { useState, useEffect } from 'react';  
import { Canvas } from '@react-three/fiber';  
import { OrbitControls } from '@react-three/drei';  
import { Vector3 } from 'three';  
  
const Scene = () => {  
  const [cameraPosition, setCameraPosition] = useState(new Vector3(0, 0, 5));  
  
  useEffect(() => {  
   const camera = new THREE.PerspectiveCamera(75, window.innerWidth / window.innerHeight, 0.1, 1000);  
   camera.position.copy(cameraPosition);  
  }, [cameraPosition]);  
  
  return (  
   <Canvas  
    style={{  
      width: '100%',  
      height: '100vh',  
      backgroundColor: 'black',  
    }}  
   >  
    <OrbitControls ref={controls} args={[camera, gl.domElement]} />  
    {/* Add 3D objects here */}  
   </Canvas>  
  );  
};  
  
export default Scene;
import React from 'react';  
import { useFrame } from '@react-three/fiber';  
import { SphereGeometry, MeshBasicMaterial } from 'three';  
  
const Planet = ({ position, radius, color }) => {  
  const mesh = useRef();  
  
  useFrame(() => {  
   mesh.current.rotation.y += 0.01;  
  });  
  
  return (  
   <mesh ref={mesh} position={position}>  
    <sphereGeometry args={[radius, 32, 32]} />  
    <meshBasicMaterial color={color} />  
   </mesh>  
  );  
};  
  
export default Planet;
import React, { useState, useEffect } from 'react';  
import axios from 'axios';  
  
const Exoplanets = () => {  
  const [exoplanets, setExoplanets] = useState([]);  
  
  useEffect(() => {  
   axios.get('https://exoplanetarchive.ipac.caltech.edu/TAP/sync?query=select+*+from+exoplanets&format=json')  
    .then(response => {  
      setExoplanets(response.data);  
    })  
    .catch(error => {  
      console.error(error);  
    });  
  }, []);  
  
  return (  
   <div>  
    {exoplanets.map(exoplanet => (  
      <Planet key={exoplanet.id} position={exoplanet.position} radius={exoplanet.radius} color={exoplanet.color} />  
    ))}  
   </div>  
  );  
};  
  
export default Exoplanets;
import React, { useState, useEffect } from 'react';  
import { useCamera } from '@react-three/fiber';  
  
const CameraFeature = () => {  
  const camera = useCamera();  
  const [isCameraEnabled, setIsCameraEnabled] = useState(false);  
  
  useEffect(() => {  
   if (isCameraEnabled) {  
    camera.lookAt(new Vector3(0, 0, 0));  
   }  
  }, [isCameraEnabled]);  
  
  return (  
   <div>  
    <button onClick={() => setIsCameraEnabled(true)}>Enable Camera</button>  
    {isCameraEnabled && (  
      <div>  
       <h2>Camera View</h2>  
       {/* Render camera view here */}  
      </div>  
    )}  
   </div>  
  );  
};  
  
export default CameraFeature;
import React from 'react';  
import Scene from './Scene';  
import Planet from './Planet';  
import Exoplanets from './Exoplanets';  
import CameraFeature from './CameraFeature';  
  
const App = () => {  
  return (  
   <div>  
    <Scene>  
      <Planet position={new Vector3(0, 0, 0)} radius={1} color={'red'} />  
      <Exoplanets />  
    </Scene>  
    <CameraFeature />  
   </div>  
  );  
};  
  
export default App;
import React, { useState, useEffect } from 'react';  
import Webcam from 'react-webcam';  
  
const CameraFeature = () => {  
  const [cameraAccessGranted, setCameraAccessGranted] = useState(false);  
  const [cameraFeed, setCameraFeed] = useState(null);  
  const [isRecording, setIsRecording] = useState(false);  
  const [recordedVideo, setRecordedVideo] = useState(null);  
  
  useEffect(() => {  
   navigator.mediaDevices.getUserMedia({ video: true })  
    .then(stream => {  
      setCameraFeed(stream);  
      setCameraAccessGranted(true);  
    })  
    .catch(error => {  
      console.error('Error accessing camera:', error);  
    });  
  }, []);  
  
  const handleTakePhoto = () => {  
   if (cameraFeed) {  
    const videoTrack = cameraFeed.getVideoTracks()[0];  
    const photo = new Image();  
    photo.src = URL.createObjectURL(new Blob([videoTrack.getFrame()], { type: 'image/jpeg' }));  
    // Handle photo capture logic here  
   }  
  };  
  
  const handleStartRecording = () => {  
   if (cameraFeed) {  
    const mediaRecorder = new MediaRecorder(cameraFeed);  
    mediaRecorder.start();  
    setIsRecording(true);  
   }  
  };  
  
  const handleStopRecording = () => {  
   if (mediaRecorder) {  
    mediaRecorder.stop();  
    setIsRecording(false);  
    // Handle recorded video logic here  
   }  
  };  
  
  return (  
   <div>  
    {cameraAccessGranted && (  
      <Webcam  
       audio={false}  
       video={true}  
       width={640}  
       height={480}  
       ref={cameraFeed}  
      />  
    )}  
    <button onClick={handleTakePhoto}>Take Photo</button>  
    <button onClick={handleStartRecording}>Start Recording</button>  
    <button onClick={handleStopRecording}>Stop Recording</button>  
    {recordedVideo && (  
      <video src={recordedVideo} controls />  
    )}  
   </div>  
  );  
};  
  
export default CameraFeature;
import React from 'react';  
import useCamera from './useCamera';  
  
const CameraFeature = () => {  
  const { cameraAccessGranted, cameraFeed } = useCamera();  
  
  if (!cameraAccessGranted) {  
   return <div>Camera access denied</div>;  
  }  
  
  // Render camera feed and handle camera logic here  
};
