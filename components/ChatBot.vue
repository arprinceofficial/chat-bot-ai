<template>
    <div>
        <!-- <button v-if="!isOpen" class="chat_btn" @click="openChatBot">Chat</button> -->
        <NuxtImg v-if="!isOpen" src="/img/Animation-Chatbot-2.gif" alt="chat" class="chat_btn" @click="openChatBot" />
        <!-- <video @click="openChatBot" class="chat_btn" autoplay muted loop  src="/img/Animation-Chatbot-2.mp4"></video> -->
        <!-- <video @click="openChatBot" class="chat_btn" autoplay muted loop playsinline >
            <source src="/img/Animation - 1710990810464.webm" type="video/webm">
        </video> -->

        <div class="chat_body" ref="chatBody">
            <div class="flex flex-col items-start justify-start w-full min-h-screen  text-gray-800 p-0 pt-10 pb-4">
                <!-- Component Start -->
                <div class="flex flex-col flex-grow w-full max-w-xl bg-white shadow-xl rounded-lg overflow-hidden">
                    <div class="flex justify-between items-center p-3"
                        :style="changeLang === 'bn-IN' ? 'background-color: #08a728' : 'background-color: #8d27db'"
                        style="transition: all 0.5s;">
                        <p class="text-white">Chat Bot</p>
                        <!-- <button class="close" @click="closeChatBot">x</button> -->
                        <i @click="closeChatBot" class="close fa fa-close" aria-hidden="true"></i>
                    </div>
                    <div class="flex flex-col flex-grow h-0 p-4 scroller">
                        <div class="scroller-content">
                            <div v-for="(chat, index) in chatBotResponse" :key="index">
                                <!-- chat text -->
                                <div v-if="chat.type === 'bot'" class="flex w-full mt-2 space-x-3 max-w-xs">
                                    <div
                                        class="flex items-center justify-center flex-shrink-0 h-10 w-10 rounded-full bg-green-500">
                                        <i class="fa-solid fa-robot text-white"></i>
                                    </div>
                                    <div>
                                        <div class="bg-green-200 p-3 rounded-r-lg rounded-bl-lg">
                                            <p class="text-sm">{{ chat.message }}</p>
                                            <button @click="playSound(chat.message)" :disabled="!playButton">
                                                <i class="fa fa-play bg-green-500 hover:bg-green-700 transition-colors duration-300 text-white px-2 py-1 rounded-lg cursor-pointer" aria-hidden="true" :class="{ 'bg-[#767676] cursor-not-allowed hover:bg-[#767676]': !playButton }"></i>
                                            </button>
                                        </div>
                                        <!-- <span class="text-xs text-gray-500 leading-none">2 min ago</span> -->
                                    </div>
                                </div>
                                <!-- Error Message -->
                                <div v-else-if="chat.type === 'error'" class="flex w-full mt-2 space-x-3 max-w-xs">
                                    <!-- <div class="flex-shrink-0 h-10 w-10 rounded-full bg-gray-300"></div> -->
                                    <div>
                                        <div class="bg-red-500 p-3 rounded-r-lg rounded-bl-lg">
                                            <p class="text-sm text-white">{{ chat.message }}</p>
                                        </div>
                                        <!-- <span class="text-xs text-gray-500 leading-none">{{ chat.message }}</span> -->
                                    </div>
                                </div>
                                <div v-else class="flex w-full mt-2 space-x-3 max-w-xs ml-auto justify-end">
                                    <div>
                                        <div class="bg-cyan-200 p-3 rounded-l-lg rounded-br-lg">
                                            <p class="text-sm">{{ chat.message }}</p>
                                            <button @click="playSound(chat.message)" :disabled="!playButton">
                                                <i class="fa fa-play bg-green-500 hover:bg-green-700 transition-colors duration-300 text-white px-2 py-1 rounded-lg cursor-pointer" aria-hidden="true" :class="{ 'bg-[#767676] cursor-not-allowed hover:bg-[#767676]': !playButton }"></i>
                                            </button>
                                        </div>
                                        <!-- <span class="text-xs text-gray-500 leading-none">2 min ago</span> -->
                                    </div>
                                    <div
                                        class="flex items-center justify-center flex-shrink-0 h-10 w-10 rounded-full bg-cyan-500">
                                        <i class="fa-regular fa-user text-white"></i>
                                    </div>
                                </div>
                            </div>
                            <!-- Chat Typing Style -->
                            <div v-if="isLoading" class="chat-bubble">
                                <div class="typing">
                                    <div class="dot"></div>
                                    <div class="dot"></div>
                                    <div class="dot"></div>
                                </div>
                            </div>
                        </div>
                    </div>
                    <form @submit.prevent="chatBot" class="flex w-full justify-center items-center bg-gray-300">
                        <div class="w-full p-3">
                            <div id="bars" class="h-10" v-if="pressButton">
                                <div class="bar"></div>
                                <div class="bar"></div>
                                <div class="bar"></div>
                                <div class="bar"></div>
                                <div class="bar"></div>
                                <div class="bar"></div>
                                <div class="bar"></div>
                                <div class="bar"></div>
                            </div>
                            <input v-else v-model="question" class="flex items-center h-10 w-full rounded px-3 text-sm"
                                type="text" placeholder="Type your message…">
                        </div>
                        <div class="flex mr-2">
                            <button type="submit" :disabled="isLoading"
                                class="flex items-center justify-center bg-indigo-500 hover:bg-indigo-600 rounded-xl text-white px-4 py-1 flex-shrink-0">
                                <!-- <span>Send</span> -->
                                <span class="ml-2 mr-2">
                                    <svg class="w-4 h-4 transform rotate-45 -mt-px" fill="none" stroke="currentColor"
                                        viewBox="0 0 24 24" xmlns="http://www.w3.org/2000/svg">
                                        <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2"
                                            d="M12 19l9 2-9-18-9 18 9-2zm0 0v-8"></path>
                                    </svg>
                                </span>
                            </button>
                            <button class="mic_icon" @click="startVoiceToText" :class="{ active: pressButton }, {'hover:bg-[#767676]':!playButton}" :disabled="!playButton" :style="!playButton ? 'cursor: not-allowed; background-color: #767676; color:black;' : 'cursor: pointer'">
                                <i data-v-cebdf746="" class="fa fa-microphone"></i>
                            </button>
                            <div @click="changeLanguage">
                                <div class="lang_icon" :class="{ active: changeLang === 'bn-IN' }">
                                    <span v-if="changeLang === 'bn-IN'">BN</span>
                                    <span v-else>EN</span>
                                </div>
                            </div>
                        </div>
                    </form>
                </div>
                <!-- Component End  -->
            </div>
        </div>
    </div>
</template>
<script setup>
const isOpen = ref(false);
const openChatBot = () => {
    isOpen.value = !isOpen.value;
    if (isOpen.value) {
        document.querySelector('.chat_body').style.right = '2rem';
    } else {
        document.querySelector('.chat_body').style.right = '-400px';
    }
}
const closeChatBot = () => {
    isOpen.value = !isOpen.value;
    document.querySelector('.chat_body').style.right = '-400px';
}
const question = ref('');
const isLoading = ref(false);
const chatBotResponse = ref([]);
const chatBot = async () => {
    try {
        if (!question.value) {
            return;
        }
        chatBotResponse.value.push({
            message: question.value,
            type: 'user'
        });
        isLoading.value = true;

        const postData = {
            // question: question.value
            message: question.value
        };
        question.value = '';
        // const response = await fetch('https://gpt.curriculum.gov.bd/chat', {
        const response = await fetch('https://api.arprince.me/api/chat-gpt', {
            method: 'POST',
            headers: {
                'Content-Type': 'application/json'
            },
            body: JSON.stringify(postData)
        });

        const json = await response.json();
        if(response.status === 200){
            chatBotResponse.value.push({
                // message: json.response,
                message: json.message,
                type: 'bot'
            });
            // console.log('json', json.response);
            await playSound(json.message);
        }else{
            chatBotResponse.value.push({
                message: 'Something went wrong please try again later!',
                type: 'error'
            });
        }
    } catch (error) {
        console.error(error);
        chatBotResponse.value.push({
            message: 'Something went wrong please try again later!',
            type: 'error'
        });
    } finally {
        isLoading.value = false;
    }
}

const playButton = ref(true);
const playSound = async (data) => {
    try {
        // playButton.value = false;
        await responsiveVoice.speak(data, "Bangla Bangladesh Male", {
            rate: 0.8,
            onstart: () => {
                playButton.value = false;
                console.log('onstart');
            },
            onend: () => {
                playButton.value = true;
                console.log('onend');
            },
            onerror: () => {
                playButton.value = true;
                console.log('onerror');
            }
        });
    } catch (error) {
        console.error("Error occurred during speaking:", error);
        playButton.value = true;
        // await responsiveVoice.speak(data, "Bangla Bangladesh Male", {
        //     rate: 0.8,
        //     onstart: () => {
        //         playButton.value = false;
        //         console.log('onstart');
        //     },
        //     onend: () => {
        //         playButton.value = true;
        //         console.log('onend');
        //     },
        //     onerror: (error) => {
        //         playButton.value = true;
        //         console.log('onerror');
        //     }
        // });
    }
}
const chatBody = ref(null);

watch(() => chatBotResponse.value, () => {
    chatBody.value.scrollTop = chatBody.value.scrollHeight;
});

// voice to text
const changeLang = ref('bn-IN');
const changeLanguage = () => {
    changeLang.value = changeLang.value === 'bn-IN' ? 'en-US' : 'bn-IN';
    recognition.lang = changeLang.value;
    // console.log('changeLang', changeLang.value);
}
const recognition = process.client ? new (window.SpeechRecognition || window.webkitSpeechRecognition)() : null;
const pressButton = ref(false);
const startVoiceToText = () => {
    pressButton.value = !pressButton.value;
    if (pressButton.value) {
        recognition.start();
    } else {
        recognition.stop();
    }
}

if (process.client) {
    recognition.lang = changeLang.value;
    recognition.continuous = false;
    recognition.interimResults = false;
    recognition.maxAlternatives = 1;

    recognition.onstart = () => {
        console.log('Voice Is Activated, You Can Speak');
        // isLoading.value = true;
    }

    recognition.onresult = (event) => {
        console.log(event)
        const current = event.resultIndex;
        const transcript = event.results[current][0].transcript;
        console.log('transcript', transcript);
        question.value = transcript;
        recognition.stop();
        pressButton.value = false;
        // isLoading.value = false;
        chatBot();
    }

    recognition.onend = () => {
        console.log('Voice Is Deactivated, You Can\'t Speak now');
        // isLoading.value = false;
        pressButton.value = false;
    }

    recognition.onerror = (event) => {
        console.log('Error occurred in recognition: ' + event.error);
        // isLoading.value = false;
        pressButton.value = false;
    }
}


</script>
<style lang="scss" scoped>
.scroller {
    overflow: auto;
    display: flex;
    flex-direction: column-reverse;
    overflow-anchor: auto !important;
}

.chat_btn {
    position: fixed;
    bottom: 40px;
    right: -15px;
    height: 120px;
    border: none;
    cursor: pointer;
    z-index: 9999;
    transition: all 1.5s;
    background: transparent;
}

.chat_body {
    position: fixed;
    top: 0;
    right: -400px;
    // right: 2rem;
    width: 400px;
    height: 100%;
    z-index: 9999999;
    transition: all 0.5s;
}

.close {
    // background-color: rgba(250, 44, 44, 0.956);
    border: 2px solid white !important;
    color: white;
    // font-weight: bold;
    border: none;
    cursor: pointer;
    padding: 0.5rem;
    border-radius: 10px;
    height: 30px;
    width: 30px;
    display: flex;
    align-items: center;
    justify-content: center;
    transition: all 0.5s;
}

// .close:hover {
//     background-color: rgba(255, 0, 0, 0.804);
//     color: white;
// }

// Chat Typing Style
.chat-bubble {
    width: 85px;
    margin: 10px 0px 5px 0px;
    background-color: #E6F8F1;
    padding: 16px 28px;
    -webkit-border-radius: 20px;
    -webkit-border-bottom-left-radius: 2px;
    -moz-border-radius: 20px;
    -moz-border-radius-bottomleft: 2px;
    border-radius: 20px;
    border-bottom-left-radius: 2px;
    display: inline-block;
}

.typing {
    align-items: center;
    display: flex;
    height: 17px;
}

.typing .dot {
    animation: mercuryTypingAnimation 1.8s infinite ease-in-out;
    background-color: #6CAD96; //rgba(20,105,69,.7);
    border-radius: 50%;
    height: 7px;
    margin-right: 4px;
    vertical-align: middle;
    width: 7px;
    display: inline-block;
}

.typing .dot:nth-child(1) {
    animation-delay: 200ms;
}

.typing .dot:nth-child(2) {
    animation-delay: 300ms;
}

.typing .dot:nth-child(3) {
    animation-delay: 400ms;
}

.typing .dot:last-child {
    margin-right: 0;
}

@keyframes mercuryTypingAnimation {
    0% {
        transform: translateY(0px);
        background-color: #6CAD96; // rgba(20,105,69,.7);
    }

    28% {
        transform: translateY(-7px);
        background-color: #9ECAB9; //rgba(20,105,69,.4);
    }

    44% {
        transform: translateY(0px);
        background-color: #B5D9CB; //rgba(20,105,69,.2);
    }
}

input:focus {
    outline: none;
}

input::placeholder {
    font-size: 12px;
}

// mic icon
.mic_icon {
    display: flex !important;
    align-items: center;
    justify-content: center;
    background-color: #E6F8F1;
    margin-left: 10px;
    padding: 12px 28px;
    -webkit-border-radius: 20px;
    -webkit-border-bottom-left-radius: 2px;
    -moz-border-radius: 20px;
    -moz-border-radius-bottomleft: 2px;
    border-radius: 20px;
    border-bottom-left-radius: 2px;
    display: inline-block;
    transition: all 0.5s;
    cursor: pointer;
}

.mic_icon:hover,
.mic_icon.active {
    background-color: #f44821;
    color: white;
}

// lang icon
.lang_icon {
    display: flex !important;
    align-items: center;
    justify-content: center;
    background-color: #8d27db;
    color: white;
    margin-left: 10px;
    padding: 12px 5px;
    -webkit-border-radius: 20px;
    -moz-border-radius: 20px;
    border-radius: 20px;
    width: 60px;
    display: inline-block;
    transition: all 0.5s;
    cursor: pointer;
}

.lang_icon.active {
    background-color: #08a728;
    color: white;
}

// voice effect
#bars {
    display: flex;
    justify-content: center;
    align-items: center;
    // background: white;
    // margin-left: 20px;
    // padding: 0px 0px 0px 0px;
}

.bar {
    background: #52467b;
    bottom: 1px;
    height: 3px;
    width: 10px;
    margin: 0px 4px;
    border-radius: 5px;
    animation: sound 0ms -600ms linear infinite alternate;
}

@keyframes sound {
    0% {
        opacity: .35;
        height: 3px;
    }

    100% {
        opacity: 1;
        height: 40px;
    }
}

.bar:nth-child(1) {
    left: 1px;
    animation-duration: 674ms;
}

.bar:nth-child(2) {
    left: 15px;
    animation-duration: 633ms;
}

.bar:nth-child(3) {
    left: 29px;
    animation-duration: 607ms;
}

.bar:nth-child(4) {
    left: 43px;
    animation-duration: 658ms;
}

.bar:nth-child(5) {
    left: 57px;
    animation-duration: 600ms;
}

.bar:nth-child(6) {
    left: 71px;
    animation-duration: 627ms;
}

.bar:nth-child(7) {
    left: 85px;
    animation-duration: 641ms;
}

.bar:nth-child(8) {
    left: 99px;
    animation-duration: 619ms;
}

.bar:nth-child(9) {
    left: 113px;
    animation-duration: 687ms;
}

.bar:nth-child(10) {
    left: 127px;
    animation-duration: 642ms;
}
</style>