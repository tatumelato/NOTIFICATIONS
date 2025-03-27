<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>User - IT Helpdesk Notifications</title>
    <style>
        * {
            box-sizing: border-box;
            margin: 0;
            padding: 0;
        }
        body {
            font-family: Arial, sans-serif;
            background-color: #111;
            color: #fff;
            text-align: center;
            padding: 20px;
        }
        .container {
            width: 100%;
            max-width: 500px;
            margin: auto;
            padding: 20px;
            background: #222;
            border-radius: 10px;
        }
        .notification {
            background: red;
            color: white;
            padding: 10px;
            border-radius: 5px;
            margin-bottom: 15px;
            display: none;
        }
        .notification-item {
            background: #333;
            border-radius: 5px;
            padding: 10px;
            margin-bottom: 10px;
            position: relative;
        }
        .notification-buttons {
            display: flex;
            justify-content: space-between;
            margin-top: 10px;
        }
        .btn {
            padding: 5px 10px;
            border: none;
            border-radius: 3px;
            cursor: pointer;
        }
        .btn-read {
            background-color: green;
            color: white;
        }
        .btn-unread {
            background-color: orange;
            color: white;
        }
        .btn-delete {
            background-color: red;
            color: white;
        }
        .note {
            color: #999;
            font-size: 0.8em;
            margin-top: 15px;
        }
    </style>
</head>
<body>
    <div class="container">
        <h2>IT Helpdesk Notifications</h2>
        <div id="notification" class="notification"></div>
        <div id="notifications-container"></div>
        <p class="note">Note: Please refresh the page to check for new notifications.</p>
    </div>

    <script>
        function showNotifications() {
            const notifications = JSON.parse(localStorage.getItem("it_updates") || "[]");
            const container = document.getElementById("notifications-container");
            container.innerHTML = ""; // Clear existing notifications

            if (notifications.length > 0) {
                document.getElementById("notification").innerText = `${notifications.length} New IT Update(s)!`;
                document.getElementById("notification").style.display = "block";

                notifications.forEach((update, index) => {
                    const notificationDiv = document.createElement("div");
                    notificationDiv.classList.add("notification-item");
                    notificationDiv.innerHTML = `
                        <h3>${update.title}</h3>
                        <p>${update.message}</p>
                        <small>Scheduled: ${update.time}</small>
                        <div class="notification-buttons">
                            <button class="btn btn-read" onclick="markAsRead(${index})">Mark as Read</button>
                            <button class="btn btn-unread" onclick="markAsUnread(${index})">Mark as Unread</button>
                            <button class="btn btn-delete" onclick="deleteNotification(${index})">Delete</button>
                        </div>
                    `;
                    container.appendChild(notificationDiv);
                });
            }
        }

        function markAsRead(index) {
            const notifications = JSON.parse(localStorage.getItem("it_updates") || "[]");
            notifications[index].read = true;
            localStorage.setItem("it_updates", JSON.stringify(notifications));
            showNotifications();
        }

        function markAsUnread(index) {
            const notifications = JSON.parse(localStorage.getItem("it_updates") || "[]");
            notifications[index].read = false;
            localStorage.setItem("it_updates", JSON.stringify(notifications));
            showNotifications();
        }

        function deleteNotification(index) {
            const notifications = JSON.parse(localStorage.getItem("it_updates") || "[]");
            notifications.splice(index, 1);
            localStorage.setItem("it_updates", JSON.stringify(notifications));
            showNotifications();
        }

        // Auto-remove notifications older than 7 days
        function cleanupNotifications() {
            const notifications = JSON.parse(localStorage.getItem("it_updates") || "[]");
            const sevenDaysAgo = new Date();
            sevenDaysAgo.setDate(sevenDaysAgo.getDate() - 7);

            const filteredNotifications = notifications.filter(update => {
                const updateDate = new Date(update.time);
                return updateDate > sevenDaysAgo;
            });

            localStorage.setItem("it_updates", JSON.stringify(filteredNotifications));
        }

        // Run on page load
        window.onload = function() {
            cleanupNotifications();
            showNotifications();
        };
    </script>
</body>
</html>
