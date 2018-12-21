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
        //shows active frame - audio/video
    }
    
    function setAudioVideoBtn(){
     // set audio/video button according to the permission
    }
    
    function activeVideoCall(){
     // set urlForConfrence for agent and visitor and pass requires params
       openVideoAudioIframe(urlforConference , urlforConferenceForAgent,'video')
      }
      
    function activeAudioCall(){
       // set urlForConfrence for agent and visitor and pass requires params
        openVideoAudioIframe(urlforConference , urlforConferenceForAgent , 'audio')
      }
      
    function openVideoAudioIframe(urlforConference , urlforConferenceForAgent, streamTyp){
    
        //check if video/audio opened if opened retrun else continue

        var dataToSend = {uid: uid , sentByAgent:false , created: new Date() };
        // check the streamTyp and set the chatBox html according to it
        if (agentPk) {
          dataToSend.user = agentPk
          if (!isAgentOnline) {
            dataToSend.user = null
          }else {
            dataToSend.user = agentPk
          }
        }
        // JSON.stringify(dataToSend) post data to database
         xhttp.open('POST', '{{serverAddress}}/api/support/supportChat/', true);
         xhttp.setRequestHeader("Content-type", "application/json");
         xhttp.send(dataToSend);
         if (threadExist==undefined) {
                dataToPublish = [uid, callType, [] , custID]  // data to publish
                details = getCookie("uidDetails");
                //if details exist push it into dataToPublish
                 var dataToSend = JSON.stringify({uid: uid , company: custID, typ: streamTyp});  //data to send to server
                 xhttp.open('POST', '{{serverAddress}}/api/support/chatThread/', true);
                 xhttp.setRequestHeader("Content-type", "application/json");
                 xhttp.send(dataToSend);
                 // in response of this request publish the data to channel
                 connection.session.publish(wamp_prefix+'service.support.agent', dataToPublish , {}, {}).then()
        }else{
      
             xhttp.open('PATCH', '{{serverAddress}}/api/support/chatThread/'+ chatThreadPk + '/', true);
             xhttp.setRequestHeader("Content-type", "application/json");
             xhttp.send(JSON.stringify({typ:streamTyp}));
             dataToPublish = [uid, callType, [] , custID, urlforConferenceForAgent]
             if (isAgentOnline) {
                  connection.session.publish(wamp_prefix+'service.support.agent.'+agentPk, dataToPublish , {}, {
                    acknowledge: true
                  }).then(function(publication) {}).catch(function(){});
             }else {
                 //agent is offline send to all
                  connection.session.publish(wamp_prefix+'service.support.agent', dataToPublish , {}, {
                    acknowledge: true
                  }).then(function(publication) {});
            }
        }
      
       //set the iFrame in HTML; and html for audio/video depending on streamTyp
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
       spying() //actively send call back to agent
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
        openChat();     // open chat window
        videoBtn.click()    //call videoBtn event listener
      })

      audioCircle.addEventListener('click',function () {
        openChat(); // open chat window
        audioBtn.click()  //call audioBtn event listener
      })
      
Showing the options to visitor - 
      
       var supportOptions = [ {name:'callCircle' , value:true} , {name:'chatCircle' , value:true}  ]; //.....and more options

        for (var i = 0; i < supportOptions.length; i++) {
            if (supportOptions[i].name=='callCircle') {
              supportOptions[i].value = callBackSupport;
            }
        //..set the values for each options true/false
        }
        
        //set  ui for supportOptions

