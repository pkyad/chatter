# chatter

    uid = getCookie("uid");
     //Check cookie 
        function checkCookie() {
          uid = getCookie("uid");
          // if exist fetch Thread or else set a new cookie with uid
       }
   
2.In fetch thread get agent pk and chat thread pk from api and fetch messages from support chat api and </br>
push data into chat messages.

3.after content loading -

    connection.onopen() ->
      register the visitor to channels - 

service.support.createDetailCookie -> 

    session.register(wamp_prefix+'service.support.createDetailCookie.'+uid, createCookieDetail).then()
  
      function createCookieDetail(args) {
        detail = getCookie("uidDetails");
       //if exists set document.cookie to this cookie
       //or else set new cookie as "uidDetails"
        }
  
  
service.support.heartbeat ->

    session.register(wamp_prefix+'service.support.heartbeat.'+uid, heartbeat).then()
    // for checking if visitor is online
     function heartbeat() {
      return uid
    }

service.support.chat ->

    session.subscribe(wamp_prefix+'service.support.chat.' + uid, supportChat).then()
  
      in supportChat callback -> check fot the arguments received by this channel such as -
  
    if(args[0]=='hideVisitorScreen'){
        // agent has called to hide visitor screen for screen sharing purpose
      }
      else if(args[0]=='ShowVisitorScreen'){
       //// agent has called to show visitor screen for screen sharing purpose
      }else if(args[0]=='ToggleVisitorVideo'){
          //agent has called to minimize video frame
      }else if(args[0]=='ShowVisitorVideo'){
        //agent has called to maximize video frame
      }

      if (args[0]=='T') {
        // indiacate the $watch on agent's type box (agent is typing)
      }
      if (args[0]=="M") {
         // message has seen sent by agent
         message = args[1] // set the args[1] to message
         setAgentDetails() //such as name and dp
      }else if (args[0]=="MF") {
       // media file has been sent by agent
        setAgentDetails() //such as name and dp
        //get the media file from api /api/support/supportChat/ 
       // xhttp.open('GET', '{{serverAddress}}/api/support/supportChat/' + args[1].filePk + '/'  , true);
 

      }else if (args[0]=='ML') {
      // media Link received
        setAgentDetails()
         message = args[1] //set the link to message
      }else if (args[0]=='AP') {
        // chat transfered 
        agentPk = args[1]; // set new agent pk 
      }else if (args[0]=='O') {
       // agent is online
      }else if (args[0]=='A') {
        // chat has been succesfully asigned
      }else if (args[0]=='F') {
        // agent has ended the chat open the feedback form 
      }
      
      //create a div element and set the innerHTML to message along with pushing it into chat.message

open the connection-

        connection.open();
        
set the UI for chatBox- 

    function createChatDiv() {
        //create body element
        //create main div
       body.appendChild(mainDiv);
    }
function we will be using- 
    
    function deactivateAudioFrame(){
     //send request to webserver for hiding the audio frame if audio has ended.
    }
    function togglingActive(element,value){
        //shows active frame - audio/vidoe
    }
    function setAudioVideoBtn(){
     // set audio/video button according to the permission
    }

 Event listeners - 
 
        // input text field 
 
     inputText.addEventListener('keyup',function(e){
       //set paper plane color on start typing
      })
     inputText.addEventListener('keydown',function(e){
        if(window.innerWidth <= 600) {
        //its mobile screen
        // normal functionality
         return
        } else {
         // desktop
         // on enter send message and on shigt+Enter give a line break
           sendMessage(inputText.value);
       }

       // read the input box of visitor
       spying()
    })
      
      // audio call button

      audioBtn.addEventListener("click",function(){
       //hide video Button
       // check that video is not active
       // check for audio state
        if(audio is not active currerntly){
          activeAudioCall()
        }else{
          deactivateAudioFrame()
          hideTheIframeOnAgentSide()
        }
        togglingActive(audioBtn,isAudioClicked)
      })
      
      // video call button

      videoBtn.addEventListener("click",function(){
        //hide audio Button
       // check that audio is not active
       // check for video state
        
        if(video is not active currerntly){
           activeVideoCall()
        }
        togglingActive(videoBtn,isVideoClicked)
      })


      videoCircle.addEventListener('click',function () {
        openChat();
        videoBtn.click()
      })

      audioCircle.addEventListener('click',function () {
        openChat();
        audioBtn.click()
      })

 
        
