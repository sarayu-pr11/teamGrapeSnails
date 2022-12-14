---
title: Response Bias Project
layout: default
description: questions
tags: [jupyter]
hide: true
---
<!-- HTML table fragment for page -->
<table>
  <thead>
  <tr>
    <th>Questions</th>
    <th>Like</th>
    <th>Dislike</th>
  </tr>
  </thead>
  <tbody id="result">
    <!-- javascript generated data -->
  </tbody>
</table>

<!-- Script is layed out in a sequence (without a function) and will execute when page is loaded -->
<script>

  // prepare HTML defined "result" container for new output
  const resultContainer = document.getElementById("result");

  const Like = "like";
  const Dislike = "dislike";

  // prepare fetch urls
  // const url = "https://fibsmath.nighthawkcoders.tk/api/questions/";
  const url = "https://fibsmath.nighthawkcoders.tk/api/questions";
  const get_url = url +"/";
  const like_url = url + "/like/";  
  const jeer_url = url + "/dislike/";  

  // prepare fetch GET options
  const options = {
    method: 'GET', // *GET, POST, PUT, DELETE, etc.
    mode: 'cors', // no-cors, *cors, same-origin
    cache: 'default', // *default, no-cache, reload, force-cache, only-if-cached
    credentials: 'same-origin', // include, same-origin, omit
    headers: {
      'Content-Type': 'application/json'
      // 'Content-Type': 'application/x-www-form-urlencoded',
    },
  };
  // prepare fetch PUT options, clones with JS Spread Operator (...)
  const put_options = {...options, method: 'PUT'}; // clones and replaces method

  // fetch the API
  fetch(get_url, options)
    // response is a RESTful "promise" on any successful fetch
    .then(response => {
      // check for response errors
      if (response.status !== 200) {
          error('GET API response failure: ' + response.status);
          return;
      }
      // valid response will have JSON data
      response.json().then(data => {
          console.log(data);
          for (const row of data) {
            // make "tr element" for each "row of data"
            const tr = document.createElement("tr");
            
            const question = document.createElement("td");
              question.innerHTML = row.id + ". " + row.question;  // add fetched data to innerHTML

            const like = document.createElement("td");
              const like_but = document.createElement('button');
              like_but.id = Like+row.id   
              like_but.innerHTML = row.like;  
              like_but.onclick = function () {
                // onclick function call with "like parameters"
                reaction(Like, like_url+row.id, like_but.id);  
              };
              like.appendChild(like_but);  

            const dislike = document.createElement("td");
              const dislike_but = document.createElement('button');
              dislike_but.id = Dislike+row.id  
              dislike_but.innerHTML = row.dislike; 
              dislike_but.onclick = function () {
                // onclick function call with "jeer parameters"
                reaction(Dislike, jeer_url+row.id, dislike_but.id);  
              };
              dislike.appendChild(dislike_but);  
             
            // this builds ALL td's (cells) into tr (row) element
            tr.appendChild(question);
            tr.appendChild(like);
            tr.appendChild(dislike);

            // this adds all the tr (row) work above to the HTML "result" container
            resultContainer.appendChild(tr);
          }
      })
  })
  // catch fetch errors (ie Nginx ACCESS to server blocked)
  .catch(err => {
    error(err + " " + get_url);
  });

  // Reaction function to likes or jeers user actions
  function reaction(type, put_url, elemID) {

    // fetch the API
    fetch(put_url, put_options)
    // response is a RESTful "promise" on any successful fetch
    .then(response => {
      // check for response errors
      if (response.status !== 200) {
          error("PUT API response failure: " + response.status)
          return;  // api failure
      }
      // valid response will have JSON data
      response.json().then(data => {
          console.log(data);
          // Likes or Jeers updated/incremented
          if (type === Like) // like data element
            document.getElementById(elemID).innerHTML = data.like;  
          else if (type === Dislike) // jeer data element
            document.getElementById(elemID).innerHTML = data.dislike;  
          else
            error("unknown type: " + type);  // should never occur
      })
    })
    // catch fetch errors (ie Nginx ACCESS to server blocked)
    .catch(err => {
      error(err + " " + put_url);
    });
    
  }

  // Something went wrong with actions or responses
  function error(err) {
    // log as Error in console
    console.error(err);
    // append error to resultContainer
    const tr = document.createElement("tr");
    const td = document.createElement("td");
    td.innerHTML = err;
    tr.appendChild(td);
    resultContainer.appendChild(tr);
  }

</script>