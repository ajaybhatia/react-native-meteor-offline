# @ajaybhatia/react-native-meteor-offline

This package uses redux and redux-persist behind the scenes to maintain offline versions of your meteor data (compatible with react-native-meteor).

## Install
`npm install @ajaybhatia/react-native-meteor-redux`

## Use
### Initialize
````javascript
import MeteorOffline from '@ajaybhatia/react-native-meteor-redux';

// initialize a MeteorOffline instance with options, currently just takes debounce
// Do this at/near the top level of your app
const GroundedMeteor = new MeteorOffline({debounce: 1000});

// Now you can access MeteorStore as a redux store throughout your app.
export {GroundedMeteor};
````

### Using cached collection

````javascript
import {GroundedMeteor} from '../index';
import Meteor, {createContainer} from '@ajaybhatia/react-native-meteor';

const component = (props) => {
  const {docs} = props;
  return (
    <View>
      {
        docs.map((doc) => {
          <Text>{doc.title}, </Text>
        });
      }
    </View>
  )
}

export createContainer((props) => {
  // MeteorOffline.subscribe takes an extra first parameter, uniqueSubscriptionName
  // The unique name allows you to have multiple subscriptions to the same publication
  // Collections are synchronized based on the uniqueSubscriptionName
  const sub = GroundedMeteor.subscribe('getUsersById', 'users/id', {userIds: [...]}, () => {
    console.log('callback');
  });
  // MeteorOffline.collection works as normal, but we pass the unique subscription name so that behind the scenes it will synchronize your collection
  return {
    docs: GroundedMeteor.collection('docs', 'getUsersById').find({}),
  };
}, component)
````

### Running the example
Execute the following commands:
````bash
cd example/RNApp && npm i
cd ../MeteorApp && meteor
cd ../RNApp && react-native run-ios
# or
cd ../RNApp && react-native run-android
````
If you get .babelrc errors, try running this in the RNApp directory
````bash
./node_modules/react-native/packager/packager.sh start --reset-cache
````
