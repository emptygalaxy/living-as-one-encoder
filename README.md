# Living As One Encoder

This library contains classes to wrap the undocumented living-as-one API for encoder functionality.  
To control any decoders, see [living-as-one-decoder](https://github.com/emptygalaxy/living-as-one-decoder).

```mermaid
graph LR
    E[Encoder] -- Internet --> S((Living As One Cloud))
    S -- Internet --> D
    
    IN[[Video source]] --> E
    D --> OUT[[Video output]]
    
    subgraph nodejs
      LAOE[living-as-one-encoder]
      LAOD[living-as-one-decoder]
    end
    LAOE -- Living As One API --> S
    LAOE -. controls .-> E
    LAOD -- RossTalk over Local Network --> D[Decoder]
    
    click LAOE "https://github.com/emptygalaxy/living-as-one-encoder" "living-as-one-encoder"
    click LAOD "https://github.com/emptygalaxy/living-as-one-decoder" "living-as-one-decoder"
```


## Installation

Install living-as-one-encoder with npm

```bash
  npm install living-as-one-encoder
```

## Features

- Contacts
  - get contacts
  - get contact
- Cues
    - get cues
    - create cue
    - create cue in real-time
    - update cue
    - delete cue
    - delete/clean-up cues that are not shared
- Encoders
    - get encoders
- Events
    - get events
    - get the event that is live
- Users
    - get users
    - get user


## Usage/Examples

```typescript
import {LivingAsOneClient} from 'living-as-one-encoder';
import * as dotenv from 'dotenv';

dotenv.config();

const client = new LivingAsOneClient();
client
    .login(
        process.env.LIVING_AS_ONE_USERNAME || '',
        process.env.LIVING_AS_ONE_PASSWORD || ''
    )
    .then(async profile => {
        if (profile) {
            console.dir(profile);

            console.log('Users:');
            console.log(await client.users.getUsers());

            console.log('Encoders:');
            console.log(await client.encoders.getEncoders());

            console.log('Events:');
            console.log(await client.events.getEvents());
            const event = await client.events.getLiveEvent();
            if (event) {
                console.log('Current live event:');
                console.dir(event, {depth: 4});

                console.log('Cues:');
                console.log(await client.cues.getCues(event.uuid));

                // create a cue for the current time
                await client.cues.createLiveCue('Test cue', false);

                // delete the cues that are not shared
                await client.cues.deleteUnsharedCues();
            }
        }
    })
    .catch(err => {
        console.error(err);
    });
```
