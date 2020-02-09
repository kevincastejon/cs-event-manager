# EventManager

### EventManager package encapsulates the delegate system into an event flow similar to the Javascript event system.
### These are main advantages:

- No delegate to declare
- Callback any method that accept an Event (or custom inherited class) object as parameter.
- The events can be dispatched without throwing exception even if no listeners are registered
- Extends easily base class Event to add properties suiting your needs

## Sources
(https://github.com/kevincastejon/cs-event-manager)[https://github.com/kevincastejon/cs-event-manager]

## Documentation
(https://github.com/kevincastejon/cs-event-manager/tree/master/Documentation/html)[https://github.com/kevincastejon/cs-event-manager/tree/master/Documentation/html]

## Usages
Simple usage, without any extends

```

        //Instantiate an EventDispatcher and add a listener
        EventDispatcher ed = new EventDispatcher();
        ed.On<Event>("someEventName", MyCallback);

        //Then somewhere in your code, call the DispatchEvent method of your EventDispatcher instance
        ed.DispatchEvent(new Event("someEventName"));

        private void MyCallback(Event e){
        Console.Writeline(e.Name+" event has been dispatched");
        }

```

Advanced usage, with extending of EventDispatcher and Event

```

        public class RiceBag : EventDispatcher
        {
            private int _maxRiceGrain = 100;
            private int _currentRiceGrainNumber = 0;

            public void AddRice(int numberOfRiceGrain)
            {
                _currentRiceGrainNumber += numberOfRiceGrain;
                if (_currentRiceGrainNumber > _maxRiceGrain) _currentRiceGrainNumber = _maxRiceGrain;
                DispatchEvent(new ContainerEvent(ContainerEvent.Names.ELEMENT_ADDED, _currentRiceGrainNumber, _maxRiceGrain));
                if (_currentRiceGrainNumber == _maxRiceGrain) DispatchEvent(new ContainerEvent(ContainerEvent.Names.FULL, _currentRiceGrainNumber, _maxRiceGrain));
            }
            public void RemoveRice(int numberOfRiceGrain)
            {
                _currentRiceGrainNumber -= numberOfRiceGrain;
                if (_currentRiceGrainNumber < 0) _currentRiceGrainNumber = 0;
                DispatchEvent(new ContainerEvent(ContainerEvent.Names.ELEMENT_REMOVED, _currentRiceGrainNumber, _maxRiceGrain));
                if (_currentRiceGrainNumber == 0) DispatchEvent(new ContainerEvent(ContainerEvent.Names.EMPTY, _currentRiceGrainNumber, _maxRiceGrain));
            }
        }
        public class ContainerEvent : Event
        {
            public enum Names { ELEMENT_ADDED, ELEMENT_REMOVED, FULL, EMPTY };
            private int _numberOfElements;
            private int _maxElements;

            public ContainerEvent(object name, int numberOfElements, int maxElements) : base(name)
            {
                _numberOfElements = numberOfElements;
                _maxElements = maxElements;
            }
            public int NumberOfElements { get { return (_numberOfElements); } }
            public int MaxElements { get { return (_maxElements); } }
            public float FillingRatio { get { return ((float)_numberOfElements / _maxElements); } }
        }
        public class TesterClass
        {
            public TesterClass()
            {
                //Instantiate a RiceBag which extends EventDispatcher and add listeners to it
                RiceBag bag = new RiceBag();
                bag.On<ContainerEvent>(ContainerEvent.Names.ELEMENT_ADDED, MyCallback);
                bag.On<ContainerEvent>(ContainerEvent.Names.ELEMENT_REMOVED, MyCallback);
                bag.On<ContainerEvent>(ContainerEvent.Names.EMPTY, MyCallback);
                bag.On<ContainerEvent>(ContainerEvent.Names.FULL, MyCallback);

                //Then anywhere in your code, add and remove some rice off the bag
                bag.AddRice(26);
                bag.AddRice(48);
                bag.RemoveRice(12);
                bag.AddRice(70);
                bag.RemoveRice(200);
            }
            private void MyCallback(ContainerEvent e)
            {
                //Monitors the events dispatched by the bag
                Console.WriteLine(e.Name + " - " + e.NumberOfElements + " rice grains on " + e.MaxElements + ". The bag is full at " + e.FillingRatio + "%");
            }
        }

```
