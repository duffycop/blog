---
layout: page
titles:
  en      : &EN       Contact
  en-GB   : *EN
  en-US   : *EN
  en-CA   : *EN
  en-AU   : *EN
  zh-Hans : &ZH_HANS  接触
  zh      : *ZH_HANS
  zh-CN   : *ZH_HANS
  zh-SG   : *ZH_HANS
  zh-Hant : &ZH_HANT  接觸
  zh-TW   : *ZH_HANT
  zh-HK   : *ZH_HANT
  ko      : &KO       연락처
  ko-KR   : *KO
key: contact
---

<style>
	label {
    		display:block;
	    	margin-top:20px;
    		letter-spacing:2px;
	}

	/* Centre the form within the page */
	form {
		    margin:0 auto;
		    width:459px;
	}

	/* Style the text boxes */
	input, textarea {
		    width:439px;
		    height:27px;
		    background:#3a3a3a;
		    border:1px solid #dedede;
		    padding:10px;
		    margin-top:3px;
		    font-size:0.9em;
		    color:#efefef;
		    -moz-border-radius:5px;
		    -webkit-border-radius:5px;
		    border-radius:5px;
	}
	input:focus, textarea:focus {
	    	border:1px solid #97d6eb;
	}
	textarea {
				height:213px;
	}
	#submit {
    width:127px;
    height:38px;
    border:none;
    text-align: center;
    margin-top:20px;
    cursor:pointer;
    display: inline-block;
    vertical-align: middle;
	}

	#submit:hover {
	    opacity:.9;
	}
</style>

<!-- Form Started -->

<form action="https://formspree.io/cmartinpf@gmail.com
" method="POST">
	<label>Name</label>
 <input type="text" name="name" placeholder="Escribe aqui tu nombre">
 <label>Email</label>
 <input type="email" name="_replyto" placeholder="Escribe aqui tu correo">
 <label>Message</label>
 <textarea name="message" placeholder="Escribe aqui tu mensaje"></textarea>
 <input class="button" id="submit" type="submit" value="Send">
</form>

<!-- Form Ended -->
