# MyMapRoom

INITIALIZATION
To initialize the map, you need to use an iframe
 

SENDING DATA
All interactions with the map are made using the window object’s message events 
To send data to the map, we use the postMessage function which is made available through the iframe element’s contentWindow object

First we get access to the iframe element using the iframe’s id
 

Then we use the postMessage function made available in the contentWindow object to send data to the map. The postMessage function takes two parameters. The first parameter is the data that we wish to send to the map and the second parameter is the src address of the iframe where the data will be sent

Each data object must contain a type key. The map receives the data object and runs its services based on the value of the type key
 

The following data objects can be sent to the map to run the desired functionalities:
*Each data object must follow these key value pairs to work correctly inside the map:
        DATA OBJECT	DESCRIPTION
  
        {
            type: 'client-code',
            clientCode : number | string
        }
	

your client code which gives access to map layers and features
 
       {
            type: 'map-iframe-id',
            mapIframeId : string
        }
	
the iframe id, used for situations where you are using multiple maps in a single page

*note that when using multiple iframes in your page, you must ensure that each iframe uses its own unique id to retrieve the iframe element used for sending data to the map

        
        {
            type: 'hide-map-tiles',
            isHideMapTiles : boolean
        }
	

the map tile images will not be displayed when set to true

        {
            type: 'tile-layer-access',
            isAccessMapTileLayer : boolean
        }
	

when set to true, the map will return information  about the current tile layer
            
         {
            type: 'send-animation-end',
            isSendAnimationEnd : boolean
        }	

when set to true, the map will return a boolean value once the initial map animation has stopped
       
        {
            type: 'get-current-center-zoom',
            getCurrentCenterAndZoom : boolean
        }
	

when set to true, the map will return the current map center coordinates and zoom level whenever the user zooms in/out or moves the map
       
        {
            type: 'decrease-zoom-control-amount',
            isDecreaseZoomControlAmount : boolean
        }
	

when set to true, the zoom accuracy of the map zoom buttons is increased
      
        {
            type:  'draw-polygon-activation',
            isDrawPolygonActive : boolean,
        }
	

when set to true, will activate the draw polygon tool on the map
        
        {
            type: 'map-attributes',
            center : { lat: number, lng: number },
            zoom: number
        }	

The center value will navigate the map to the position of the given coordinates,  and the zoom value will zoom the map to the given amount
        DATA OBJECT	DESCRIPTION
        
        {
            type:   'single-marker',
            marker : {lat: number, lng: number}
        }
	

will display a marker at the given coordinate
       
       {
            type:   'markers-info',
            markersData : {
                  latitude: number,
                  longitude: number,
                  clientMarkerId?: number | string,
                  modalBody?: string,
                  fixedModalBody?: string,
                  color?: string;
               }[ ]
        }
	


will use the array of objects stored in markersData to display a marker at each latitude and longitude coordinate. The modalBody will be displayed as a popup when the marker is clicked. The fixedModalBody will be displayed as a fixed popup.

*note that for both the modalBody and fixedModalBody values you can send a string of HTML elements if you wish to apply your own custom styles to the content inside the popup



      
      
        {
            type: 'single-polygon-info',
            singlePolygonCoords: {lat: number, lng: number} [ ],
            isAuthorizedToEdit?: boolean,
            isEditable?: boolean,
            showBreakpoints?: boolean,
        }
	
will display a polygon on the map using the coordinates array stored in singlePolygonCoords. The isAuthorizedToEdit value can be used to authorize a user for accessing the map editing tools. The isEditable value determines whether the specified polygon can be edited. If the showBreakpoints value is set to true, all the polygon vertices will be numerically displayed over the polygon.

*note that if you want the map to navigate to the polygon automatically, you must also send a separate ‘map-attributes’ message containing the polygon’s center coordinates and zoom level

        
        {
            type:  'multi-polygons-info',
            polygonsData : {
                            center?: {lat: number, lng: number},
                            polygonCoords: {lat: number, lng: number} [ ],
                            polygonId: number,
                            color?: string,
                            opacity?: number,
                        } [ ],
        }
	




will display a set of polygons on the map based on the given polygonCoords value
     
        {
            type:  'edited-polygon-id',
            editedPolygonID : number,
        }
	

if you wish to edit one of the polygons in the polygonsData array that was sent in the ‘multi-polygons-info’ message, you need to send the desired polygon’s id to the map which will activate the edit button
        



        

        DATA OBJECT	DESCRIPTION
        
         {
            type:   'map-options',
            options :  {
                        attributes: {
                            canZoom: boolean
                        },
                        tools: {
                            tiles: boolean,
                            currentCoords: boolean,
                            measureTool: boolean,
                            breakpoints: boolean,
                            drawPolyline: boolean,
                            drawPolygon: boolean,
                            dragControl: boolean,
                        },
                        editControl: boolean,
                        tileLayer: boolean,
                        buttons: {
                            fullScreen: boolean,
                            zoom: boolean,
                        },
                        theme: {
                            markerColor: string,
                        },
                        mapLocked: boolean,
                    }
        }
	













for more direct control over the available map services


RETRIEVING DATA 
Just as we can send data to the map, we can also receive data being sent from the map. To retrieve this data and use it in your application you must access the “message” event that is provided by the global window object. Each time the postMessage function is used inside the map for sending data, the callback function of the “message” event will be executed and you will have access to that data inside of the event’s data property.

 


By checking the value of the type property inside the data object, you can determine which dataset has been received and use it in your application however you wish
 

The following data objects can be retrieved from the map inside your application:
        DATA OBJECT	DESCRIPTION
  
          

            “map-mounted” 	
*note that for this particular data you will be receiving a string instead of an object, its only purpose is to notify your application that the map has mounted onto the DOM and is ready for use

 

                
         {
              type: 'map-clicked',
              currentCoords: { lat: number, lng: number },
         }
	 

data received each time the click event is executed inside of the map
       
        {
             type: 'animation-stopped',
             isStopped: boolean
        }
	
will be sent by the map once its initial zoom animation has finished executing

*note that you will only receive this data if you have triggered the service inside the map using the ‘send-animation-end’ data object 

        {
           type: 'current-center-zoom',
           currentMapId: number,
           currentZoom: number,
           currentCenter: {lat: number, lng: number},
       }
	

this data is sent each time the zoom and zoomend event are executed in the map. It will give you access to the current zoom level and center coordinates.

*note that you will only receive this data if you have triggered the service inside the map using the ‘get-current-center-zoom’ data object
     
       {
          type: 'polygon-drawn',
          drawnPolygonCoords:{lat: number, lng: number}[ ],
          drawnPolygonPerimeter: number,
          drawnPolygonArea: number,
          drawnPolygonCenter: {lat: number, lng: number},
       }
	


this data is sent by the map each time a polygon has been drawn

        DATA OBJECT	DESCRIPTION
  
       {
            type: 'polygon-edited',
            editedPolygonCoords: {lat: number, lng: number}[ ],
            editedPolygonArea: number,
            editedPolygonCenter: {lat: number, lng: number}, 
            editedPolygonPerimeter: number
       }
	

	

this data is sent by the map each time a polygon has been edited
       
       {
            type: 'polygon-dragged,
            editedPolygonCoords: {lat: number, lng: number}[ ],
            editedPolygonArea: number,
            editedPolygonCenter: {lat: number, lng: number}, 
            editedPolygonPerimeter: number
       }
	



this data is sent by the map each time a polygon’s location has been changed after being dragged using the drag tool


