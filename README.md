using Microsoft.AspNetCore.SignalR;
using System.Threading.Tasks;

namespace SignalRDemo
{
    public class MessageHub : Hub
    {
        public override async Task OnConnectedAsync()
        {
            await Clients.All.SendAsync("ReceiveMessage", "Hello world!");
            await base.OnConnectedAsync();
        }
    }
}
