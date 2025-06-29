## Strategy pattern
Its like inheritance but for behaviour (methods)
```py
# Without Strategy Pattern (bad)
class Cinema:
    def book_seats(self, num_seats, strategy_type="default"):
        if strategy_type == "front_to_back":
            # allocation logic here
        elif strategy_type == "best_available":
            # different logic here
        elif strategy_type == "consecutive":
            # more logic here
        # This gets messy with more strategies!

# With Strategy Pattern (good)
class SeatAllocationStrategy:
    def allocate(self, available_seats, num_seats):
        pass

class FrontToBackStrategy(SeatAllocationStrategy):
    def allocate(self, available_seats, num_seats):
        # Front row first logic
        return selected_seats

class BestAvailableStrategy(SeatAllocationStrategy):
    def allocate(self, available_seats, num_seats):
        # Center seats first logic
        return selected_seats

class Cinema:
    def __init__(self, strategy: SeatAllocationStrategy):
        self.allocation_strategy = strategy
    
    def book_seats(self, num_seats):
        return self.strategy.allocate(self.available_seats, num_seats)

# Usage
cinema = Cinema(FrontToBackStrategy())
cinema.book_seats(3)
```

## Factory pattern
Static method that belongs to class so no need to initialise that class to make an instance out of it
```py
# Without Factory (bad)
# You have to remember all the details everywhere
imax = Cinema("Movie", 20, 30, premium_rows=[1,2,3], vip_rows=[4,5])
standard = Cinema("Movie", 15, 25, premium_rows=[1,2], vip_rows=[])

# With Factory Pattern (good)
class CinemaFactory:
    @staticmethod
    def create_imax_cinema(movie_title):
        return Cinema(
            movie_title=movie_title,
            total_rows=20,
            seats_per_row=30,
            premium_rows=[1,2,3,4,5],
            vip_rows=[6,7,8],
            strategy=BestAvailableStrategy()
        )
    
    @staticmethod
    def create_standard_cinema(movie_title):
        return Cinema(
            movie_title=movie_title,
            total_rows=15,
            seats_per_row=25,
            premium_rows=[1,2],
            vip_rows=[],
            strategy=FrontToBackStrategy()
        )

# Usage
imax = CinemaFactory.create_imax_cinema("Avengers")
standard = CinemaFactory.create_standard_cinema("Rom Com")
```

## Observer pattern
i think its useful to solve Problem: When seats are booked/released, multiple things need to update (UI, inventory, notifications)
```py
# Without Observer (bad)
class Cinema:
    def book_seats(self, num_seats):
        # Book the seats
        booking = self.create_booking(seats)
        
        # Manually update everything (tight coupling!)
        self.update_display()
        self.send_confirmation_email()
        self.update_inventory()
        self.log_booking()
        # What if you forget one?

# With Observer Pattern (good)
class BookingObserver:
    def on_booking_created(self, booking):
        pass

class EmailNotifier(BookingObserver):
    def on_booking_created(self, booking):
        self.send_confirmation_email(booking)

class InventoryTracker(BookingObserver):
    def on_booking_created(self, booking):
        self.update_inventory(booking)

class Cinema:
    def __init__(self):
        self.observers = []
    
    def add_observer(self, observer):
        self.observers.append(observer)
    
    def book_seats(self, num_seats):
        booking = self.create_booking(seats)
        
        # Notify all observers
        for observer in self.observers:
            observer.on_booking_created(booking)

# Usage
cinema = Cinema()
cinema.add_observer(EmailNotifier())
cinema.add_observer(InventoryTracker())
```

## State pattern
```py
# Without State Pattern (bad)
class Seat:
    def __init__(self):
        self.status = "available"
    
    def book(self):
        if self.status == "available":
            self.status = "booked"
        elif self.status == "held":
            self.status = "booked"
        elif self.status == "booked":
            raise Exception("Already booked")
        # Complex if-else logic everywhere!

# With State Pattern (good)
class SeatState:
    def book(self, seat):
        pass
    def hold(self, seat):
        pass
    def release(self, seat):
        pass

class AvailableState(SeatState):
    def book(self, seat):
        seat.state = BookedState()
    def hold(self, seat):
        seat.state = HeldState()

class HeldState(SeatState):
    def book(self, seat):
        seat.state = BookedState()
    def release(self, seat):
        seat.state = AvailableState()

class BookedState(SeatState):
    def book(self, seat):
        raise Exception("Already booked")

class Seat:
    def __init__(self):
        self.state = AvailableState()
    
    def book(self):
        self.state.book(self)
    
    def hold(self):
        self.state.hold(self)
```
