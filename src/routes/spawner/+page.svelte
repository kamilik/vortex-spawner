<script>
//	import { LOGNAME } from "$env/static/private";
	import { getAllContexts } from "svelte";
	import { linear } from "svelte/easing";

	// @ts-nocheck

	// import { LOGNAME } from "$env/static/private";

	// import { LOGNAME, LOGNAME } from "$env/static/private";
</script>

<svelte:head>
	<title>Dashboard</title>
	<meta name="description" content="Account control" />
</svelte:head>

<div class="parent">
	<div class="toggle-container">
		<div id="dev" class="toggle-button active switch2 dev" data-env="dev" onclick="change_period2('dev')">DEV
		</div>
		<div id="stg" class="toggle-button switch2 stg" data-env="stg" onclick="change_period2('stg')">STG</div>
		<div id="prod" class="toggle-button switch2 prod" data-env="prod" onclick="change_period2('prod')">PROD
		</div>
		<div id="selector" class="selector"></div>

	</div>
</div>

<div class="text-column">
	<div class="popup-box">
		<div class="popup">
			<div class="content">
				<header>
					<p>Add a new account</p>
					<i class="fa-solid fa-xmark"></i>
				</header>
				<form action="#">
					<div class="row title">
						<label for="">Name</label>
						<input type="text" name="" id="name" /><br /><br />
						<label for="">Version</label>
						<input type="text" name="" id="version" /><br /><br />
						<label for="">Priority</label>
						<input type="text" name="" id="priority" /><br /><br />
					</div>
					<div class="row description"></div>
					<button on:click={sendData} >Add account</button>
				</form>
			</div>
		</div>
	</div>
	<div class="wrapper" id="wrapper">
		
		<li class="add-box">
			<div class="icon"><i class="fa-solid fa-plus">+</i></div>
			<p>Add new account</p>
		</li>
		
	</div>

	<script>
         		window.onload = function () {
					selector.style.left = 0;
        			selector.style.width = dev.clientWidth + 'px';
        			selector.innerHTML = 'DEV';
					selector.style.backgroundColor = 'var(--color-theme-1)';
					// fetchDataAndDisplay(default)
		 };

        

		function change_period2(period) {
    var dev = document.getElementById('dev');
    var stg = document.getElementById('stg');
    var prod = document.getElementById('prod');
    var selector = document.getElementById('selector');
    
    if (period === 'dev') {
        selector.style.left = 0;
        selector.style.width = dev.clientWidth + 'px';
        selector.innerHTML = 'DEV';

        
    } else if (period === 'stg') {
        selector.style.left = dev.clientWidth + 'px';
        selector.style.width = stg.clientWidth + 'px';
        selector.innerHTML = 'STG';


        
    } else {
        selector.style.left = dev.clientWidth + stg.clientWidth + 1 + 'px';
        selector.style.width = prod.clientWidth + 'px';
        selector.innerHTML = 'PROD';


        
    }
    selector.style.backgroundColor = 'var(--color-theme-1)';

    
}

const apiUrlMap = {
    dev: 'https://spawner.dev.s.vortex-internal.foundation',
    stg: 'https://spawner.stg.s.vortex-internal.foundation',
    prod: 'https://spawner.prod.s.vortex-internal.foundation'
};

const wrapper = document.getElementById('wrapper');

function fetchDataAndDisplay(env) {
    const apiUrl = apiUrlMap[env];
    fetch(apiUrl + "/api/client/list", {
        method: 'POST',
        headers: {
            'Content-Type': 'application/json'
        },
        body: JSON.stringify({})
    })
        .then(response => {
            if (!response.ok) {
                throw new Error('Network response was not ok');
            }
            return response.json();
        })
        .then(data => {
            try {
                if (data.clients && Array.isArray(data.clients)) {
                    wrapper.innerHTML = ''; // Clear previous data
					wrapper.appendChild(addBox);
                    data.clients.forEach((client, index) => {
                        const listItem = document.createElement('div');
                        listItem.classList.add('client');
                        listItem.innerHTML = `
						
						<li class="note">
                            <div class="details">
                                <div class="circle"></div>
                                <p>${client.client_name}</p>
                                <span>
                                    id: ${client.id}<br>
                                    version: ${client.version}<br>
                                    cpu: ${client.cpu}<br>
                                    memory: ${client.memory}<br>
                                    priority: ${client.priority}<br>
                                    commit-hash: ${client.image.split(':').pop()}<br>
                                </span>
                            </div>
                            <div class="bottom-content">
                                <span>${ParsTime(client.spawned_at)}</span>
                                <div class="settings">
                                    <i onclick="showMenu(this)" class="fa-solid fa-ellipsis iconel"></i>
                                    <ul class="menu">
                                        <li onclick="editClient(${index},'${client.client_name}','${client.image}')"><i class="fa-light fa-pen"></i>Edit</li>
                                        <li onclick="deleteClient(${index})"><i class="fa-duotone fa-trash"></i>Delete</li>
                                    </ul>
                                </div>
                            </div>
						</li>	
                        `;
						
                        wrapper.appendChild(listItem);
						
                    });
					

                } else {
                    throw new Error('Invalid response format: clients array missing');
                }
            } catch (error) {
                console.error('Error parsing response:', error);
            }

        })
        .catch(error => {
            console.error('Error fetching data:', error);
        });
}

function toggleButton(button) {
    const buttons = document.querySelectorAll('.toggle-button');
    buttons.forEach(btn => btn.classList.remove('active'));
    button.classList.add('active');
}

document.addEventListener('DOMContentLoaded', () => {
    const buttons = document.querySelectorAll('.toggle-button');
    const defaultButton = document.querySelector('.toggle-button[data-env="dev"]');
    toggleButton(defaultButton);
    const env = defaultButton.dataset.env;
    fetchDataAndDisplay(env);

    buttons.forEach(button => {
        button.addEventListener('click', () => {
            toggleButton(button);
            const env = button.dataset.env;
            fetchDataAndDisplay(env);
        });
    });
});


		// TIME
		function ParsTime(strTime) {
			const time = new Date(strTime);
			const months = [
				'January',
				'February',
				'March',
				'April',
				'May',
				'June',
				'July',
				'August',
				'September',
				'October',
				'November',
				'December'
			];
			h = time.getHours();
			min = time.getMinutes();
			d = time.getDate();
			m = months[time.getMonth()];
			let outTime = `</b>` + ' ' + d + ' ' + m + ' ' + h + ':' + min;
			return outTime;
		}


	function sendData() {
    const nameInput = document.getElementById('name');
    const versionInput = document.getElementById('version');
    const priorityInput = document.getElementById('priority');

    const name = nameInput.value;
    const version = parseInt(versionInput.value);
    const priority = parseInt(priorityInput.value);

//    if (!name || isNaN(version) || isNaN(priority)) {
//        console.error('Invalid input data');
//        return;
//    }

    const env = document.querySelector('.toggle-button.active').dataset.env;
 //   const apiUrl = apiUrlMap[env];


    fetch(apiUrl+"/api/client/add", {
        method: 'POST',
        headers: {
            'Content-Type': 'application/json'
        },
        body: JSON.stringify({
            "client_name": name,
        	"version": version,
        	"priority": priority

        }),
    })
    .then(response => {
        if (!response.ok) {
            throw new Error('Network response was not ok');
        }
        console.log('Client added successfully');
        // Дополнительные действия при успешном ответе
    })
    .catch(error => {
        console.error('Error adding client:', error);
    });
}

		const addBox = document.querySelector('.add-box');
		const popupBox = document.querySelector('.popup-box');

		const closeBox = popupBox.querySelector('header i');

		let ClientName = popupBox.querySelector('#name').value;
		let ClientVersion = popupBox.querySelector('#version').value;
		let ClientPriority = popupBox.querySelector('#priority').value;

		const addBtn = popupBox.querySelector('button');

		const notes = JSON.parse(localStorage.getItem('notes') || '[]');

		const menuel = document.querySelector('.iconel');



		function deleteNote(noteId) {
			notes.splice(noteId, 1);

			localStorage.setItem('notes', JSON.stringify(notes));
			showNotes();
		}

		function editNote(noteId, title, description) {
			titleTag.value = title;
			descTag.value = description;
			addBox.click();

			deleteNote(noteId);
			// console.log(noteId)
		}

		addBox.onclick = () => popupBox.classList.add('show');
		closeBox.onclick = () => {

			popupBox.classList.remove('show');
		};

		addBtn.onclick = (e) => {
			console.log(ClientName, ClientVersion, ClientPriority);

                localStorage.setItem('notes', JSON.stringify(json.clients));
                closeIcon.click();
		};

	</script>
</div>
