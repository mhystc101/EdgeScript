# EdgeScript
NOT MY SCRIPT

(function() {
    'use strict';

    const config_elements = ['skip_videos_tickbox', 'auto_advance_tickbox', 'skip_intro_tickbox', 'guess_practice_tickbox', 'vocab_completer_tickbox', 'show_column_tickbox', 'debug_console_tickbox'];
    const features = [
        { name: 'Skip Videos', id: 'skip_videos_tickbox' },
        { name: 'Auto Advance', id: 'auto_advance_tickbox' },
        { name: 'Skip Intro', id: 'skip_intro_tickbox' },
        { name: 'Guess Practice', id: 'guess_practice_tickbox' },
        { name: 'Vocab Completer', id: 'vocab_completer_tickbox' },
        { name: 'Show Right Column', id: 'show_column_tickbox' },
        { name: 'Debug Console', id: 'debug_console_tickbox' }
    ];

    const overlay = create_overlay();
    const config_window = create_config_window(overlay);
    create_close_button(config_window);
    create_title(config_window, 'Configuration');
    features.forEach(feature => config_window.appendChild(build_menu_entry(feature.name, feature.id)));
    create_socials_button(config_window);  // Add the socials button to the config menu
    create_tweaks_button();

    // Create a textarea element for debug console output
    var debug_console = document.createElement('textarea');
    debug_console.setAttribute('id', 'debug_console');
    debug_console.style.cssText = 'position: fixed; bottom: 10px; left: 10px; background-color: rgba(0, 0, 0, 0.8); color: #fff; border-radius: 5px; padding: 10px; width: 400px; height: 200px; font-family: monospace; resize: none; z-index: 9999;';
    debug_console.readOnly = true;

    // Append the debug console to the document body
    document.body.appendChild(debug_console);

    setInterval(loop, 1000);
    load_config();

    function create_overlay() {
        return create_element('div', {
            style: 'width:100vw;height:100vh;position:fixed;top:0;left:0;z-index:9998;background-color:rgba(0,0,0,0.5);display:none;',
            id: 'overlay'
        }, document.body);
    }

    function create_config_window(parent) {
        return create_element('div', {
            style: 'width:300px;height:400px;position:fixed;top:50%;left:50%;transform:translate(-50%,-50%);background-color:white;z-index:9999;border:1px solid black;padding:10px;display:flex;flex-direction:column;align-items:center;justify-content:start;border-radius:10px;box-shadow:0px 0px 15px rgba(0,0,0,0.3);overflow-y:auto;',
            id: 'config_window'
        }, parent);
    }

    function create_close_button(parent) {
        create_element('button', {
            innerText: 'Close',
            style: 'margin-bottom:20px;padding:10px 20px;border:none;background-color:#444;color:white;border-radius:5px;cursor:pointer;',
            onclick: () => {
                config_window.style.display = 'none';
                overlay.style.display = 'none';
            }
        }, parent);
    }

    function create_title(parent, text) {
        create_element('h2', { innerText: text, id: 'config_title' }, parent);
    }

    function create_tweaks_button() {
        create_element('button', {
            innerText: 'Tweaks',
            id: 'tweaks_button',
            style: 'position:fixed;top:10px;left:50%;transform:translateX(-50%);z-index:10000;border:none;background-color:#ff9800;color:white;padding:10px 20px;border-radius:30px;font-size:16px;cursor:pointer;box-shadow:0px 4px 6px rgba(0,0,0,0.1);transition:background-color 0.3s,box-shadow 0.3s;',
            onmouseover: () => {
                tweaks_button.style.backgroundColor = '#e68900';
                tweaks_button.style.boxShadow = '0px 6px 8px rgba(0,0,0,0.2)';
            },
            onmouseout: () => {
                tweaks_button.style.backgroundColor = '#ff9800';
                tweaks_button.style.boxShadow = '0px 4px 6px rgba(0,0,0,0.1)';
            },
            onclick: () => {
                config_window.style.display = 'flex';
                overlay.style.display = 'block';
            }
        }, document.body);
    }

    function build_menu_entry(name, id) {
        const container = create_element('div', { style: 'display:flex;justify-content:space-between;margin:10px 0;width:100%;' });
        create_element('label', { innerText: name }, container);
        create_element('input', { type: 'checkbox', id }, container);
        return container;
    }

    function create_socials_button(parent) {
        create_element('button', {
            innerText: 'Socials/Credits',
            style: 'margin-top:20px;padding:10px 20px;border:none;background-color:#007bff;color:white;border-radius:5px;cursor:pointer;',
            onclick: show_socials_screen
        }, parent);
    }

    function show_socials_screen() {
        config_window.innerHTML = ''; // Clear current config window content
        create_close_button(config_window); // Add the close button to the socials screen
        create_title(config_window, 'Credits');
        create_element('p', { innerHTML: 'Steam: <a href="https://steamcommunity.com/profiles/76561198265104876" target="_blank">76561198265104876</a>' }, config_window);
        create_element('p', { innerHTML: 'YouTube: <a href="https://www.youtube.com/dylanhook" target="_blank">dylanhook</a>' }, config_window);
        create_element('p', { innerHTML: 'Discord: <a href="https://discord.gg/pN5fzxaEt5" target="_blank">Join Discord</a>' }, config_window);
        create_element('button', {
            innerText: 'Go Back',
            style: 'margin-top:20px;padding:10px 20px;border:none;background-color:#444;color:white;border-radius:5px;cursor:pointer;',
            onclick: () => {
                config_window.innerHTML = ''; // Clear current config window content
                create_close_button(config_window); // Add the close button back
                create_title(config_window, 'Configuration');
                features.forEach(feature => config_window.appendChild(build_menu_entry(feature.name, feature.id)));
                create_socials_button(config_window);  // Add the socials button to the config menu
            }
        }, config_window);
    }

    function create_element(tag, attributes, parent) {
        const element = document.createElement(tag);
        Object.assign(element, attributes);
        if (attributes.innerHTML) {
            element.innerHTML = attributes.innerHTML;  // Ensure innerHTML is set properly
        }
        parent && parent.appendChild(element);
        return element;
    }

    function load_config() {
        config_elements.forEach(id => {
            const value = localStorage.getItem(id);
            if (value === 'true') {
                document.getElementById(id).checked = true;
            }
        });
        toggle_debug_console();
    }

    function sync_config() {
        config_elements.forEach(id => {
            localStorage.setItem(id, document.getElementById(id).checked);
        });
        toggle_debug_console();
    }

    function loop() {
        skip_videos();
        skip_intro();
        vocab_completer();
        show_column();
        sync_config();
        auto_advance_if_elapsed_time_reached_total();
    }
    
    function skip_videos() {
        if (is_checked('skip_videos_tickbox')) {
            click('.play-button');
            click('.video-close-button');
        }
    }
    
    // Variable to store the last observed elapsed time
    var last_elapsed_time = '';
    
    // Variable to store the total time
    var total_time = '';
    
    // Variable to store the time when the timer last moved
    var last_time_moved = new Date().getTime();
    
    // Function to output text to the debug console
    function write_to_debug_console(text) {
        if (is_checked('debug_console_tickbox')) {
            debug_console.value += text + '\n';
        }
    }
    
    // Function to execute auto_advance function
    function auto_advance_if_elapsed_time_reached_total() {
        var current_time = new Date().getTime();
        var elapsed_since_last_move = current_time - last_time_moved;
        var iframe = document.getElementById('stageFrame');
    
        if (iframe) {
            var iframe_document = iframe.contentDocument || iframe.contentWindow.document;
            var timer_element = iframe_document.getElementById('uid1_time');
    
            if (timer_element) {
                var timer_text = timer_element.textContent;
                var times = timer_text.split(' / ');
                var elapsed_time = times[0];
                var total_time = times[1];
    
                if (elapsed_time === total_time && elapsed_time !== last_elapsed_time) {
                    write_to_debug_console('Elapsed time has reached total time.');
                    auto_advance();
                    guess_practice();
                } else if (elapsed_time !== last_elapsed_time) {
                    last_time_moved = new Date().getTime();
                } else if (elapsed_since_last_move > 3000) {
                    write_to_debug_console('Timer has stopped moving for 3 seconds.');
                    auto_advance();
                    guess_practice();
                }
    
                last_elapsed_time = elapsed_time;
            } else {
                write_to_debug_console('Timer element not found in the iframe.');
                auto_advance();
                guess_practice();
            }
        } else {
            write_to_debug_console('Iframe not found.');
            auto_advance();
            guess_practice();
        }
    }
    
    
    // Function to observe changes in the timer element
    function observe_timer_changes(timer_element) {
        // Create a MutationObserver to watch for changes in the timer element
        var observer = new MutationObserver(function(mutations_list, observer) {
            // Whenever the content of the timer element changes, extract and output the new elapsed time and total time
            var timer_text = timer_element.textContent;
            var times = timer_text.split(' / ');
            var elapsed_time = times[0];
            total_time = times[1];
            write_to_debug_console('New elapsed time: ' + elapsed_time);
            write_to_debug_console('Total time: ' + total_time);
        });
        // Start observing changes to the timer element's content
        observer.observe(timer_element, { subtree: true, character_data: true, child_list: true });
    }
    
    // Function to extract timer information from the iframe content
    function extract_timer_info_from_iframe() {
        write_to_debug_console('Script started...');
        // Get the iframe element
        var iframe = document.getElementById('stageFrame');
        if (iframe) {
            // Try to find the timer element every second
            setInterval(function() {
                // Access the contentDocument of the iframe
                var iframe_document = iframe.contentDocument || iframe.contentWindow.document;
                // Find the timer element within the iframe content
                var timer_element = iframe_document.getElementById('uid1_time');
                if (timer_element) {
                    // If the timer element is found, stop checking and start observing changes
                    clearInterval(timer_check_interval);
                    observe_timer_changes(timer_element);
                }
            }, 1000);
        } else {
            write_to_debug_console('Iframe not found.');
        }
    }
    
    // Call the extract_timer_info_from_iframe function immediately
    extract_timer_info_from_iframe();
    
    // Function to execute auto_advance function
    function auto_advance() {
        if (!is_checked('auto_advance_tickbox')) {
            return
        }
            var activity_title = document.getElementById("activity-title").innerText;
            if (activity_title !== "Quiz") {
                try {
                    document.querySelector('.footnav.goRight').click();
                } catch (TypeError) {}
                try {
                    window.frames[0].API.FrameChain.nextFrame();
                } catch (TypeError) {}
                
                var frame_right_links = document.querySelectorAll('li.FrameRight a');
                frame_right_links.forEach(function(link) {
                    link.click();
                });
            }
    }
    
    function skip_intro() {
        if (is_checked('skip_intro_tickbox')) {
            try { window.frames[0].document.getElementById('invis-o-div').remove(); } catch (e) {}
        }
    }
    
    function guess_practice() {
        if (!is_checked('guess_practice_tickbox')) {
            return
        }
        try {
            const activity_title = document.getElementById('activity-title').innerText;
            if (activity_title === 'Assignment') return;
    
            if (['Instruction', 'Warm-Up', 'Practice'].includes(activity_title)) {
                const options = window.frames[0].frames[0].document.getElementsByClassName('answer-choice-button');
                if (options.length) {
                    options[Math.floor(Math.random() * options.length)].click();
                }
                try { window.frames[0].API.Frame.check(); } catch (e) {}
            }
        } catch (e) {}
    }

    function vocab_completer() {
        if (is_checked('vocab_completer_tickbox') && document.getElementById('activity-title').innerText === 'Vocabulary') {
            try {
                window.frames[0].document.getElementsByClassName('word-textbox')[0].value = window.frames[0].document.getElementsByClassName('word-background')[0].innerText;
                for (const button of window.frames[0].document.getElementsByClassName('playbutton vocab-play')) {
                    button.click();
                }
                click('.uibtn.uibtn-blue.uibtn-arrow-next', window.frames[0].document);
            } catch (e) {}
        }
    }

    function show_column() {
        if (is_checked('show_column_tickbox')) {
            try { window.frames[0].frames[0].document.getElementsByClassName('right-column')[0].children[0].style.display = 'block'; } catch (e) {}
            try { window.frames[0].frames[0].document.getElementsByClassName('left-column')[0].children[0].style.display = 'block'; } catch (e) {}
        }
    }

    function is_checked(id) {
        return document.getElementById(id).checked;
    }

    function click(selector, parent = document) {
        try { (parent || document).querySelector(selector).click(); } catch (e) {}
    }

    function toggle_debug_console() {
        if (is_checked('debug_console_tickbox')) {
            debug_console.style.display = 'block';
        } else {
            debug_console.style.display = 'none';
        }
    }
})()
