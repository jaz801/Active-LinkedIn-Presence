/*
 this linkedin script
 
 clicks on random amount of post on Linkedin Like
 
 afterwards it scroll to the post with the most amount of comments
 
 So you can add you comments if you want gain the most visibility with one comment. 
 
 You still have to write the comment youself.
 
 copy pase this in to the javascrip console.   top of chrome -> view -> developer -> javascript console 
*/




function scrollAndCountButtons() {
    // Scroll down 2000 pixels
    window.scrollBy(0, 2000);

    // Wait for the scroll to complete
    setTimeout(function() {
        // Define the target node
        const targetNode = document.body;

        // Create a function to count the buttons
        const countButtons = () => {
            // Select all buttons with the specific class and aria-label
            let buttons = document.querySelectorAll('button[aria-label="React Like"].artdeco-button.artdeco-button--muted.artdeco-button--4.artdeco-button--tertiary.react-button__trigger');
            
            // Output the count of the buttons
            console.log('Number of buttons:', buttons.length);

            // If there are buttons, choose a random number to press
            if (buttons.length > 0) {
                pressRandomButtons(buttons);
                // Stop observing after the initial count and action
                observer.disconnect();
            }
        };

        // Create a function to press a random number of buttons
        const pressRandomButtons = (buttons) => {
            // Determine the number of buttons to press (at least one)
            let numberOfButtonsToPress = Math.floor(Math.random() * buttons.length) + 1;
            console.log(`Pressing ${numberOfButtonsToPress} buttons`);

            // Shuffle the buttons array
            let shuffledButtons = Array.from(buttons).sort(() => 0.5 - Math.random());

            // Randomize the order of the buttons to press
            let buttonsToPress = shuffledButtons.slice(0, numberOfButtonsToPress).sort(() => 0.5 - Math.random());

            // Function to click buttons with a random delay
            const clickButtonsWithDelay = (buttons, index) => {
                if (index < buttons.length) {
                    // Randomize scroll speed between 50% and 75% of normal speed
                    const scrollSpeed = Math.random() * (0.75 - 0.5) + 0.5;

                    // Scroll button into view with randomized speed
                    buttons[index].scrollIntoView({ behavior: 'smooth', block: 'center', inline: 'nearest' });
                    setTimeout(() => {
                        buttons[index].click();
                        const randomDelay = Math.random() * 1000 + 2000; // Random delay between 2000ms and 3000ms
                        setTimeout(() => clickButtonsWithDelay(buttons, index + 1), randomDelay);
                    }, scrollSpeed * 1000); // Adjust the delay as needed to ensure smooth scrolling and clicking
                } else {
                    // After all buttons are clicked, find and scroll to the span with the highest number of comments
                    findAndScrollToMaxComments();
                }
            };

            // Start clicking buttons with delay
            clickButtonsWithDelay(buttonsToPress, 0);
        };

        // Create a MutationObserver to detect changes in the DOM
        const observer = new MutationObserver(mutationList => {
            mutationList.forEach(mutation => {
                if (mutation.type === 'childList') {
                    mutation.addedNodes.forEach(node => {
                        if (node.nodeType === 1) { // Ensure the node is an element
                            countButtons();
                        }
                    });
                }
            });
        });

        // Function to find and scroll to the span with the highest number of comments
        const findAndScrollToMaxComments = () => {
            // Select all spans containing comments
            let commentSpans = document.querySelectorAll('span[aria-hidden="true"]');

            // Filter out spans that contain the word 'comments'
            let commentElements = Array.from(commentSpans).filter(span => span.textContent.includes('comments'));

            // Find the span with the highest number of comments
            let maxComments = 0;
            let maxCommentElement = null;
            commentElements.forEach(span => {
                let commentCount = parseInt(span.textContent.replace(/\D/g, ''), 10);
                if (commentCount > maxComments) {
                    maxComments = commentCount;
                    maxCommentElement = span;
                }
            });

            // Scroll to the span with the highest number of comments
            if (maxCommentElement) {
                maxCommentElement.scrollIntoView({ behavior: 'smooth', block: 'center', inline: 'nearest' });
            }
        };

        // Start observing the target node for configured mutations
        observer.observe(targetNode, {
            childList: true,
            subtree: true
        });

        // Initial count
        countButtons();
    }, 1000); // Adjust the timeout as needed
}

// Call the function
scrollAndCountButtons();



