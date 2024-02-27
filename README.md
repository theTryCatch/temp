import { Injectable } from '@angular/core';
import { HubConnection, HubConnectionBuilder } from '@microsoft/signalr';

@Injectable({
  providedIn: 'root'
})
export class SignalRService {
  private hubConnection: HubConnection;

  constructor() {
    this.buildConnection();
  }

  private buildConnection() {
    this.hubConnection = new HubConnectionBuilder()
      .withUrl('http://localhost:5000/messageHub') // Replace with your SignalR server URL
      .build();
  }

  startConnection() {
    this.hubConnection.start()
      .then(() => console.log('Connection started'))
      .catch(err => console.log('Error while starting connection:', err));
  }

  onReceiveMessage(callback: (message: string) => void) {
    this.hubConnection.on('ReceiveMessage', (message: string) => {
      callback(message);
    });
  }
}
