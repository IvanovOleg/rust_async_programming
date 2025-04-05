# Channels
Channels is the way of passing data between threads.

## Real-life examples:

### Single-producer Single-consumer
Passing a note in school. One student writes a note and passes it to another student. Student who wrote the note is a producer. Sutednt who received a not is a consumer. And the note is the data passed through the channel.

### Multiple-producer Single-consumer
Teacher collects home-work. A bunch of students produce their home-work and pass it to the one teacher. The home-work is the data, sutends are producers and the teacher is a consumer.

### Single-producer Multiple-consumer
Public announcement system. Speacker system which spreads information throughout the school. Microfone is a producer. Speackers are consumers. Announcement is the data.

### Multiple-producer Multiple-consumer
Public announcement system. Speacker system which spreads information throughout the school. Microfones are producers. Speackers are consumers. Announcement is the data.


## Tokio Channels
Is an efficient way to pass the data between threads.
