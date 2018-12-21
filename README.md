
1. Check Cookie - 

        uid = getCookie("uid");
         //Check cookie 
            function checkCookie() {
              uid = getCookie("uid");
              // if exist fetch Thread or else set a new cookie with uid 
                    fetchThread(uid);
           }
   
2.In fetch thread get agent pk and chat thread pk from api and fetch messages=

        function fetchThread(uid) {
          xhttp.onreadystatechange = function() {
              if (this.readyState == 4 && this.status == 200) {
                var data = JSON.parse(this.responseText)
                if (data.length>0) {
                  threadExist = true
                  chatThreadPk = data[0].pk
                  agentPk = data[0].user
                }
                fetchMessages(uid);
              } else if (this.responseText == '{"PARAMS":"createCookie"}') {
                document.cookie = encodeURIComponent("uid") + "=deleted; expires=" + new Date(0).toUTCString()
                uid = new Date().getTime()
                setCookie("uid", uid, 365);
                fetchMessages(uid);
              }
          };

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
    
       function endChat() {
            chatClosed = true
            // check if feedback form opend if not than continoue
            // send patch request to chatThread with data require
            openFeedback() // open feedback form
       }


       function openFeedback(id) {

            // hide audio/video button
            // create a div for offline message

            var div = document.createElement("div");
            div.id="offlineMessage"
            div.innerHTML =  '<div style="margin:0px 0px 10px..........              
            messageBox.appendChild(div);
            scroll();
            // create feedback stars 
            submitStarForm(id);
       }

      function thankYouMessage() {
        var div = document.createElement("div");
        div.id="thankYou"
        div.innerHTML = '<div style="margin:0px 0px 10px; ...
        messageBox.appendChild(div);
        scroll();
      }

      function submitStarForm(id) {
        submitCancel.addEventListener('click',function(){
            thankYouMessage();
           // handle the ui stuff
           // patch request to chatThread with updated datatoSend
            xhttp.open('PATCH', '{{serverAddress}}/api/support/chatThread/'+ chatThreadPk + '/', true);
      })
      
      function hideTheIframeOnAgentSide(){
        connection.session.publish(wamp_prefix+'service.support.agent.'+agentPk, [uid , 'hideTheIframe' ] , {}, {
          acknowledge: true
        }).then(function(publication) {});
      }

      function hideAudioAndVidoeBtn(){
        //hide audio/video button
      }
      
      function getCurrentURL(){
        // pass current url to channel for current activity of visitor
            connection.session.publish(wamp_prefix+'service.support.agent.'+agentPk, [uid , 'CU' , currentUrl] , {}, {
          acknowledge: true
        }).
        then(function(publication) {});
      }
      
      function scroll() {
        setTimeout(function () {
          var id = document.getElementById("chatBox_content");
          id.scrollTop = id.scrollHeight;
        }, 200);
      }

      function onlineAgent() {
        if (agentPk) {
            // call service.support.heartbeat with agent pk and check the response
             },
             function (err) {
              // agaent is offline
            }
           );
        }
      }
      
      function spying(inputVal) {
        // publising to service.support.agent with args['T']
          connection.session.publish(wamp_prefix+'service.support.agent.'+agentPk, [uid , 'T' , inputVal] , {}, {}).
          then(function(publication) {});
       }
       
      function sendMessage(inptText) {

            // if input length is less than 1 - return
            // get uid from cookie
            // check if inptText contains you tube url
   
            if (youtubeLink) {
                status = "ML";
                link = "https://www.youtube.com/embed/" + inptText.split("v=")[1];
                    // set data to send
            }else {
              // status is normal message
              var dataToSend = {uid: uid , message: inptText , sentByAgent:false , created: new Date() };
              // set data to send
              //create a div element
                div.innerHTML = messageDiv(message)
                 messageBox.appendChild(div);
                scroll();
                chat.messages.push(message);
                inputText.value ='';
                // post data to "/api/support/supportChat/"

            var dataToPublish = [uid , status , message ];
            // push required data into dataToPublish
            // post data to /chatThread and in response of the post publish dataToPublish to agentPK.
        }


 Event listeners - 
 
 
     inputText.addEventListener('keyup',function(e){                                  // input text field 
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
   

      audioBtn.addEventListener("click",function(){                                 // audio call button
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
      

      videoBtn.addEventListener("click",function(){                                  // video call button
        //hide audio Button
       // check that audio is not active
       // check for video state
        
        if(video is not active currerntly){
           activeVideoCall()
        }
        togglingActive(videoBtn,isVideoClicked)
      })
      
    

      videoCircle.addEventListener('click',function () {                                  // video Circle
        openChat();     // open chat window
        videoBtn.click()    //call videoBtn event listener
      })
      

      audioCircle.addEventListener('click',function () {
        openChat(); // open chat window
        audioBtn.click()  //call audioBtn event listener
      })
      
      
      exitBtn.addEventListener("click", function() {                                // exit Button
        // close the support 
          if (confirm("Are you sure you want to end this chat ?")) {
            getFrameContent.postMessage('userleft',webRtcAddress ); 
            // sends message to webRTC about user left
            endChat();  // chat ends
            hideAudioAndVidoeBtn()  // hide audio/video
          }
        }
      }, false);
      
      
      paperPlane.addEventListener("click", function() {                            // paper plane in input text
        //set focus on input text area
        sendMessage(inputText.value); // call send message
      }, false);
      
      
      paperClip.addEventListener("click", function() {                             // paper clip in input text
        filePicker.click();  // call html id filePicker
      }, false);
      
      
      closeSupport.addEventListener("click", function() {                           // close Support button
            endOfConversation();
            // handel UI
            }
      }, false);
      

      closeIcon.addEventListener("click", function() {                              // close Icon
            //top close icon display only in smaller screen
        //handle UI
        }
      } , false);

      
      
Showing the options to visitor - 
      
       var supportOptions = [ {name:'callCircle' , value:true} , {name:'chatCircle' , value:true}  ]; //.....and more options

        for (var i = 0; i < supportOptions.length; i++) {
            if (supportOptions[i].name=='callCircle') {
              supportOptions[i].value = callBackSupport;
            }
        //..set the values for each options true/false
        }
        
        //set  ui for supportOptions
        
Check if single service for company - 

         //set  ui for supportOptions
         
create a css style sheet- 
    
       function setStyle={
            var newStyle = document.createElement('style');
                    newStyle.appendChild(document.createTextNode("\

                     .SyrowFont {\
                     font-family: 'Syrow';\
                     font-weight: normal;\
                      .....
                        }\
                      .font-Syrow24hSupport:before {\
                          content: '\\61';\
                      }\

                   .....................
               )
            document.head.appendChild(newStyle);
       }
        setStyle();
        
 start new conversastion - 
 
      startNewChatBtn.addEventListener("click" , function () {
    
        // clear innerHTML for messageBOX and reset the agent details , create new cookie with uid
          //  pushMessages()
            // setAudioVideoBtn();
                // handle ui
        connection._transport.close() // clsoe the active connection

        connection.onclose = function(reason, details) {
          var connection = new autobahn.Connection({url: '{{wampServer}}', realm: 'default'}); //create a new connection
          connection.open()
        }
    })
    
Handling all the window messages - 

      window.addEventListener("message", receiveMessage, false);

      function receiveMessage(event){
      
        if (event.data=='loadMyOrders') {
          var url = 'https://chrome.google.com/webstore/detail/screen-capturing/ajhifddimkapgcifgcodmmfdlknahffk'
           window.open(url);
        }
        if (event.data=='calledToHideVideo') {
          setIframeRotated()
          connection.session.publish(wamp_prefix+'service.support.agent.'+agentPk, [uid , 'calledToHideVideo' ] , {}, {
            acknowledge: true
          }).
          then(function(publication) {});
        }
        if (event.data=='calledToShowVideo') {
          setIframeToNormal()
          connection.session.publish(wamp_prefix+'service.support.agent.'+agentPk, [uid , 'calledToShowVideo' ] , {}, {
            acknowledge: true
          }).
           then(function(publication) {});
        }
        if (event.data=='hideTheMainFrame') {
            // hide the main frame
        }else if(event.data=='showTheMainFrame'){
            // show the main frame
        }
        if (event.data== 'replyToUseruserleft'){
         //user left call endOfConversation()
        }
        if (event.data== 'dintPickTheCall'){
          // agnet dint pick the call
        }
      }
  checking for agent to be online - 
  
      setInterval(function(){
        onlineAgent();
      },10000 )
